# Network Domain Model

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

There are also compatibility issues relating to both legacy system models based on the Network Domain Model, and to the SPYDERISK system-modeller service. These force an unusual approach to the use of branches and versioning in GitHub. See the README in the 'main' branch of this repository for details.

Domain models were originally expressed in OWL/RDF, but human readability became very poor. The contents of this model are now normally edited by loading into a relational database, and using queries and forms to provide a more 'joined up' few of the moving parts. This also makes it easier to check for flaws that may exercise known bugs in system-modeller, or that make the domain model self-inconsistent.

Today, the source format comprises a set of CSV files, each of which corresponds to a table when loaded into an editor. Simple changes can be made directly in the CSV files, making it easy to fix typos and simply logical consistency issues.

## Build and package

A GitHub Action is executed when a release is made or when manually triggered. It converts from the source CSV files into an NQ serialisation using csv2nq and bundles this with the icon files and icon-mapping file to create an artifact that can be used in the system-modeller. The package is stored in the Maven repository of GitHub Packages.

## Model contents and coverage

This version of the Network Domain Model covers the following areas.

### Basics

Basic socio-cyber-physical assets and relationships are included, describing

- human assets, i.e., users (strictly user roles) and other human or organizational stakeholders
- physical spaces
- devices (hosts) and communication networks (IP, Bluetooth)
- application assets, consisting of processes and data

Note that data assets model the presence of data in the system, allowing its relationships to processes and users to be specified. A data asset is not a copy of the data - for that, a separate, inferred 'data lifecycle' asset is used (see below).

### Data lifecycle inference

Inference rules cause further data assets to be created representing copies of the data in a system. These model:

- serialised copies stored on devices
- serialised copies transferred between processes in messages
- copies loaded into memory by processes

The inference rules also generate data flow relationships, which can be mapped to network paths. This is important when deciding if data could be subject to attack - one needs to know where copies may be, so potential attack paths can be identified.

### Network connectivity inference

Inference rules insert network interfaces between hosts and subnets to which they are connected. Interfaces assets represent targets for some types of attacks. Hosts with connections to multiple subnets may function as gateways, routing messages between subnets. These routes are modelled as inferred network path 'segment' assets. Interfaces and segments are also used to model network routing policies, as they correspond to IP Tables or Netfilter 'tables', whose rules are models using controls associated with those assets.

Network paths are then inferred as collections of 'segments' (hence the name for a gateway routing table asset - a 'path' is made up of 'segments'). Process-process communication is  inferred to use any available path, and thus is the potential exposure of communicated data determined.

Note that when a gateway provides a subnet (i.e., acts as a router enabling and controlling message routing for that subnet), the inference rules assume it is a private subnet (since usually that will be the case). The inference rules assume the router will behave as a NAT device, so by default no inbound connections are possible from subnets not provided by that router. The rules assume that port forwarding (or equivalent) is used to override this and allow access by legitimate clients to services on the subnet.

### Virtualisation

Virtual hosts are included in the model. These must be provisioned by some other host, but can be stacked (so a physical host provisions a virtual host that provisions another virtual host). Threats represent the propagation of certain threat effects up and down the stack, e.g., by transferring overload downwards, and loss of availability upwards.

### Rights and contextualisation

Access rights are represented in terms of 'trustworthiness attributes', whose level reflects the trustworthiness of anyone (insider or external attacker) possessing the related rights. Threats are used to model how these rights are related, e.g., the attribute 'Control' for a Host asset represents possession of admin rights, with threats representing the way these can be used to assume user-level access rights, take control of processes running on the Host, or access data stored there.

Suppose an exploit on a Host allows an attacker to gain admin rights. The SPYDERISK system modeller would infer these other threats may follow, thus creating threat paths leading to possible consequences throughout the system. Attacks are thus represented by these threat paths, usually including threats representing malicious actions, combined with other threats representing exploitation of rights gained, and sometimes secondary effects.

One challenge is that if an attack to gain admin rights involved (say) physical access to a notebook PC when left unattended in an Internet cafe, it should not then be possible to access data that may be accessed via this host only when connected to a private LAN in another location. To ensure such unrealistic threat paths are not created, access rights must be represented using inferred assets associated with both the host, and a physical location or a network connection. Trustworthiness attributes like 'Control' or 'UserTW' are associated with these contexts, rather than simply with the host or process to which those rights apply.

In practice, this creates a problem for users of system-modeller, because to model a situation where a host has been compromised, they must specify a low TW level for these attributes for the host and some or all the associated contexts. Equally, if they wish to specify that such a compromise has an impact, they must specify an impact level for the host and/or associated contexts. Having TW levels or behaviour likelihoods propagate between the host and its contexts is not possible or threat paths would no longer be confined to the context of the initial breach. To solve this problem, additional 'Local' TW attributes and behaviours were introduced, with secondary threats providing appropriate propagation mechanisms such that the behaviour at a host has the likelihood of the worst affected context, while the TW level at the host is the best case TW level across any context.

![image](https://github.com/Spyderisk/domain-network/assets/27415349/1adaa830-5c32-4759-9e14-2f991c61f04a)

### Internet of Things

Two types of IoT Thing are included: Sensors (which sense their physical environment) and Controllers (which modify the environment).

Each must be modelled as a combination of a host device, plus (communicating) processes and data. This ensures that threats to devices, processes and data will be applied appropriately to every Thing.

To simplify this for SPYDERISK system modeller users, inference rules are used to insert the processes and data for each Thing added to a system model. There are some extra 'surfacing' threats that propagate threat effects from the processes and data to the (asserted) Thing asset, so these effects show up on the visible asset that was added by the user.

### Clouds

A Data Centre asset can be used to reprsent a collection of servers on a LAN provided by a router in a physical location. This in itself does not represent a Cloud data centre - it is just a shorthand allowing this bunch of assets to be added as one.

Cloud services are then modelled by allowing virtual hosts and processes to be related directly to the Data Centre, rather than via its embedded hosts. This signifies that it is a Cloud data centre, running a management framework that provides a degree of isolation and may support service level agreements to limit consumption, etc.

On top of that, virtual host subclasses represent 'Kubernetes' style management concepts including Pods and Containers. If these are used, inference rules will also insert a Kubernetes master and slave hosts, a virtual LAN connecting these, and further virtual subnets connecting Containers that are in the same Pod, etc.

## Contributors

Those who have contributed to the IT Network domain model are:

* Mike Surridge
* Stephen Phillips
* Samuel Senior
