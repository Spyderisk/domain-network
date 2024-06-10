# IT Network Model

## Introduction

This is a Spyderisk domain model describing IT networks, their users and physical environment. It can be deployed to a [Spyderisk system-modeller](https://github.com/Spyderisk/system-modeller) service, allowing the service to be used to create and analyse models of IT systems and applications as socio-cyber-physical systems.

The source files for the most complete version of this domain model can be found in the [6a branch](https://github.com/Spyderisk/domain-network/tree/6a) and pre-built [packages for installation](https://github.com/Spyderisk/domain-network/packages/1826148) containing the RDF in NQ format along with icon sets are available.

This README explains considerations, compatibility issues and their implications for version management and repository structure.

## Considerations

A Spyderisk domain model is a knowledge base, in which several different considerations must be balanced against each other:

- approach: how are IT networks represented in detail (given basic invariants like users, locations, devices and networks)?
- coverage: what features and variations are supported?
- usability: what is the balance between rules used to analyse system models, and rules to help users create system models?
- flexibility: how far do user-assistance rules constrain the composition of system models?
- fidelity: how detailed and accurate is the representation of a system/application?
- complexity: how complex and difficult to analyse is it?

In addition, two distinct compatibility issues must be considered:

- system-modeller: which version(s) of the SPYDERISK system-modeller service are required/supported?
- system models: will system-modeller users be able to reanalyse their system models without any changes?

The compatibility issues mean it is often necessary to develop multiple branches at the same time. Each may be needed by a specific group of users who may have legacy system models they can't afford to modify, specific requirements regarding the balance between (say) fidelity and complexity, or system modeller instances that cannot easily be upgraded.

## Versions

Versions of the domain model intended for use (beyond development, experimentation and testing) are tagged with a version string of the form GXN-F-B, where:

- G is a numeric 'generational' identifier signifying the overall approach, which typically becomes more complex from generation to generation
- X is a letter indicating membership of a SPYDERISK system-modeller compatibility set within that generation of network domain models
- N is a number indicating membership of a system model compatibility set within that generation
- F is a number which changes when new features are added
- B is a number which changes when bugs are fixed with no new features

It is important to realise that the X and N compatibility indicators do not form a hierarchy. It is possible for two domain models to support and/or require different versions of the SPYDERISK system-modeller, yet both be able to support the same system models, or vice versa. Historically, whenever there was a system-modeller compatibility change (i.e., a change in X), we reset the system model compatibility indicator back to '1'. Ideally, multiple system model compatibility sets should not be created for each major version of system-modeller, but sometimes this can't be avoided.

## Repository structure

For these reasons, the conventional repo structure with 'master', 'dev' and several development branches is not used. 

The equivalent of 'dev' branches in this repository are named according to their G and X version indicators. At the time this project was moved to GitHub, the latest version intended to be used beyond development/experimentation was '6a1-2-12', which will become the first HEAD of branch '6a'. If a subsequent update cannot be used without changing system models, branches will be created for '6a1' and '6a2', If system-modeller is improved and a new domain model is needed to work with that or support the new system-modeller features, then a new '6b' branch will be created.

Merging changes between these branches is very difficult without causing further compatibility issues. Useful changes in one branch will normally have to be backported manually into the other branches, if at all. The branches must therefore be regarded as more like distinct developments, rather than different versions of the same development.

As such, it is expected that each new branch will have its own README, explaining its purpose, content and (critically) its compatibility constraints.

Inevitably, development of the domain model itself tends to focus more on the later branches. The need for older branches fades as system-modeller installations and system models are updated, and old branches may become stale. We will decide later whether those branches should be locked or deleted if/when that happens.

## Contributors

Those who have contributed to the IT Network domain model are:

* Mike Surridge
* Samuel Senior
