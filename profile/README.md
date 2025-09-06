# Sunflower Mono-Super-repo – Top-level Architecture

This repository hosts several related projects (vision, sdk, grpc, central-ci, etc.). The diagram below captures the intended hub-and-spoke architecture across the broader organization, showing shared hubs (contracts, CI, base images, libraries, security, observability), domain spokes (Vision, LoRa, Kinematics, Intelligence, Viz), and edge/infra services. Not every box maps 1:1 to a current directory here; this is the planned/organizational view.

```mermaid
graph LR
  %% Hub Repos center
  core[core-contracts proto/schemas]
  ci[central-ci reusable workflows]
  imgs[platform-images base OCI]
  libs[shared-libs Go: runtime, math, transport]
  sec[security-policies]
  obs[observability logging/metrics conventions]

  %% Spokes: Vision
  subgraph Vision
    vcamera[vision-camera]
    vio[vision-io]
    vdet1[vision-detector-yolov8]
    vdet2[vision-detector-<model>]
    vtrack1[vision-tracker-kcf]
    vtrackN[vision-tracker-<algo>]
  end

  %% Spokes: LoRa
  subgraph LoRa
    lradio[lora-radio]
    lgw[lora-gateway]
  end

  %% Spokes: Kinematics
  subgraph Kinematics
    kcore[kinematics-core]
    klegs[kinematics-legs]
    ksolve[kinematics-solver]
  end

  %% Spokes: Intelligence
  subgraph Intelligence
    igait[intelligence-gait]
    iplan[intelligence-planner]
    ifuse[intelligence-fusion]
  end

  %% Spokes: Viz
  subgraph Viz
    vweb[viz-web]
    vcli[viz-cli]
  end

  %% Edge/Infra services
  mqtt[mqtt]
  grpc[grpc edge/cloud services]
  sdk[sdk client SDKs]
  tuploader[telemetry-uploader]

  %% Hub dependencies
  vcamera --> libs
  vio --> libs
  vdet1 --> libs
  vdet2 --> libs
  vtrack1 --> libs
  vtrackN --> libs
  lradio --> libs
  lgw --> libs
  kcore --> libs
  klegs --> libs
  ksolve --> libs
  igait --> libs
  iplan --> libs
  ifuse --> libs
  vweb --> libs
  vcli --> libs
  mqtt --> libs
  grpc --> libs
  tuploader --> libs
  sdk --> libs

  %% Contracts as the single source of truth
  vcamera --> core
  vio --> core
  vdet1 --> core
  vdet2 --> core
  vtrack1 --> core
  vtrackN --> core
  lradio --> core
  lgw --> core
  kcore --> core
  klegs --> core
  ksolve --> core
  igait --> core
  iplan --> core
  ifuse --> core
  vweb --> core
  vcli --> core
  mqtt --> core
  grpc --> core
  sdk --> core
  tuploader --> core

  %% CI and platform images apply org-wide
  vcamera --> ci
  vio --> ci
  vdet1 --> ci
  vdet2 --> ci
  vtrack1 --> ci
  vtrackN --> ci
  lradio --> ci
  lgw --> ci
  kcore --> ci
  klegs --> ci
  ksolve --> ci
  igait --> ci
  iplan --> ci
  ifuse --> ci
  vweb --> ci
  vcli --> ci
  mqtt --> ci
  grpc --> ci
  sdk --> ci
  tuploader --> ci

  %% Base images dependency build/runtime bases
  vcamera --> imgs
  vdet1 --> imgs
  lgw --> imgs
  grpc --> imgs
  mqtt --> imgs
  vweb --> imgs

  %% Security & Observability conventions
  vcamera -.-> sec
  lgw -.-> sec
  grpc -.-> sec
  mqtt -.-> sec
  vweb -.-> sec

  vcamera -.-> obs
  lgw -.-> obs
  grpc -.-> obs
  mqtt -.-> obs
  vweb -.-> obs

  %% Bridges and flows
  lgw --> mqtt
  mqtt --> grpc
  vweb --> grpc
  tuploader --> grpc

  %% Styling optional
  classDef hub fill:#222,color:#fff,stroke:#555;
  class core,ci,imgs,libs,sec,obs hub;
```
