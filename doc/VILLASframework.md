# High-level Architecture {#framework-architecture}

Design objective for VILLAS framework: a holistic framework with modular and generic architecture.

# Integration Layers {#framework-architecture-layers}

Three integration layers are foreseen based on the diversity of the facilities and applications:

- Hard Real-Time Integration Layer (H-RTIL)
- Soft Real-Time Integration Layer (S-RTIL)
- Offline Integration Layer (O-IL)

@image html Generic_VILLAS_Architecture_mapping.svg VILLAS integration layers. width=75%

## Hard Real-Time Integration Layer (H-RTIL)

- Integrates digital real-time simulators, actual hardware devices and test benches into a joint experiment
- H-RTIL requires interfaces that provide deterministic and synchronized data exchange between laboratories
- Real-time protocols and physical point-to-point connections should be used

## Soft Real-Time Integration Layer (S-RTIL)

- Integration of tools and applications with less critical timing requirements (inaccurate timing or time-varying delays are acceptable to some extent)
- User interaction via web-based access
- High-level tools, system level control, optimization applications

## Offline Integration Layer (O-IL)

- Integration of domain-specific analyses that are performed at pre-experiment and post-experiment phases
- An interface to adapt parameters or models of experiments in a pre-experiment phase
- Non-real-time tasks during the experiment, such as data logging for Data as a Service

# Example scenarios {#framework-architecture-scenarios}

The following diagrams show example configuration of VILLASframework components as they have been used in various demonstrations and experiments:

@image html VILLASnode_large.svg Two site scenario with VILLASfpga. width=100%

@image html VILLASframework.svg VILLASframework. width=100%

@image html VILLASframework_ERIC_lab.svg ERIClab scenario. width=100%

@image html VILLASframework_RT_SuperLab.svg RT-SuperLab scenario. width=100%