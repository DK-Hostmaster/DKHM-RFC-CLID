![DK Hostmaster Logo](https://www.dk-hostmaster.dk/sites/default/files/dk-logo_0.png)

# DKHM RFC for Client ID support for EPP

![Markdownlint Action](https://github.com/DK-Hostmaster/DKHM-RFC-CLID/workflows/Markdownlint%20Action/badge.svg)
![Spellcheck Action](https://github.com/DK-Hostmaster/DKHM-RFC-CLID/workflows/Spellcheck%20Action/badge.svg)

## Table of Contents

<!-- MarkdownTOC bracket=round levels="1,2,3,4" indent="  " autolink="true" autoanchor="true" -->

- [Introduction](#introduction)
  - [About this Document](#about-this-document)
  - [XML and XSD Examples](#xml-and-xsd-examples)
- [Description](#description)
- [XSD Definition](#xsd-definition)
- [References](#references)

<!-- /MarkdownTOC -->

<a id="introduction"></a>
## Introduction

This is a draft and proposal for implementation of the process for domain name and contact creation via the DK Hostmaster EPP portal/service. The specification briefly touches on the registrar portal service (RP), which mimicks the EPP service for consistency.

The overall [description of the concept][CONCEPT] of the registrar model offered by DK Hostmaster A/S provised a an general overview, where this RFC digs into the details of the Client-ID in the context of an implementation proposal.

The registrar model offered by DK Hostmaster, gives registrars the option to manage a customer’s .dk domain name if the customer would prefer this. We call this "registrar management". Where the model to allow the customer to manage their own domain name themselves, as they do today, is referred to as "registrant management".

<a id="about-this-document"></a>
### About this Document

We have adopted the term RFC (_Request For Comments_), due to the recognition in the term and concept, so this document is a process supporting document, aiming to serve the purpose of obtaining a common understanding of the proposed implementation and to foster discussion on the details of the implementation. The final specification will be lifted into the [DK Hostmaster EPP Service Specification][DKHMEPPSPEC] implementation and this document will be closed for comments and the document no longer be updated.

<a id="xml-and-xsd-examples"></a>
### XML and XSD Examples

All example XML files are available in the [DK Hostmaster EPP XSD repository][DKHMXSDSPEC].

The proposed extensions and XSD definitions are available in the  [3.2 candidate][DKHMXSD3.2] of the DK Hostmaster XSD, which is currently a draft and work in progress and marked as a  _pre-release_.

<a id="description"></a>
## Description

DK Hostmaster are extending the registry capabilities to support _one-stop-shop_ so registrars and registrants will have the option to pick the model for domain name administration with suit their requirements.

As for the proposal for support for the EPP transfer command, described in the RFC: "[DKHM RFC for Transfer Domain EPP Command][DKHMRFCTRANSFER]", support for specifying the sponsoring client has to be supported for several of the EPP commands:

- `create domain`
- `create contact`
- `create host`

The goal is to support the standard to the extent possible. Changing the behavior might not suit everybody and this proposal aims to address this particular challenge.

## Domain Creation, Maintenance and Deletion

The default behavior of the EPP `create domain` command as described in [RFC:5731][RFC5731], will attach the client-ID (`CLID`) of the authenticated party to the object created.

Having the client-ID specified at this time indicates that the domain name is under registrar management from creation. To change the registrar and discontinue the registrar management will require a transfer, as described in the RFC: "[DKHM RFC for Transfer Domain EPP Command][DKHMRFCTRANSFER]".

If the registrar does not want to create domains under registrar management the default behaviour can be defined in RP, where the available are:

- registrar management
- registrant management

The setting will be global and will influence the behaviour in both:

- EPP
- RP

These settings are controlling the account as a whole for all relevant commands.

- `create domain`, this section
- `create contact`, described below
- `create host`, described below

## Contact Creation, Maintenance and Deletion

The default behavior of the EPP `create contact` command as described in [RFC:5733][RFC5733], will attach the client-ID (`CLID`) of the authenticated party to the object created, just as for the domain creation described above.

The contact object will be under the sponsoring party throughout it's _life-cycle_ and transfer of contact objects will not be explicitly supported. See our proposal for transfer support described in our RFC: "[DKHM RFC for Transfer Domain EPP Command][DKHMRFCTRANSFER]".

As for the `create domain` command (above) the default behaviour can be defined in RP. Where the option "registrant management", will create contact objects sponsored by DK Hostmaster instead instead of the registrar.

Deletion will not be supported and will work as it currently is implemented in the DK Hostmaster EPP service and described in the specification. See the section: "[Unimplemented commands](https://github.com/DK-Hostmaster/epp-service-specification#unimplemented-commands)" for details. Contact objects are automatically delete, under the following policy:

- The contact object is not in use
- It holds not roles/association with other objects
- The associated financial account holds a balance of 0
- It has been inactive for 45 days

The maintenance, meaning changes and updates to data, will also be limited. DK Hostmaster locks contact objects for changes if these have been matched to a register for name and address information, this applies to:

- Danish citizens of the type individual, bound to the CPR register
- Danish companies, public organizations and associations of the types, bound to the CVR register

The following contact types are also limited, due to the VAT number validation:

- EU companies, public organizations and associations of the types, bound to the EU Vies register

All other types has to be maintained by the sponsoring client, with the exception of the name attribute.

## Host Creation, Maintenance and Deletion

The default behavior of the EPP `create host` command as described in [RFC:5732][RFC5732], will attach the client-ID (`CLID`) of the authenticated party to the object created.

The `create host` command will be limited in that sense that it will not be possible to create a host object, which is subordinate to a domain name (superordinate), which is sponsered by another registrar. This limitation will only be enformed for domain names under the `.dk` TLD. All domain names under other TLDs will be subject to this limitation.

As for the `create domain` and `create contact` commands (above) the default behaviour can be defined in RP. Where the option "registrant management", will create host objects sponsored by DK Hostmaster instead of the registrar.

Responsibility and privileges for maintenance (`update host`) of the host object is assigned to the name server administrator as described in [the DK Hostmaster EPP Service specification](https://github.com/DK-Hostmaster/epp-service-specification#create-host).

If the name server responsible is allocated to the registrar account (group), this can be handled via RP and EPP.

The deletion of host objects are under a similar regime, as specified in [the DK Hostmaster EPP Service specification](https://github.com/DK-Hostmaster/epp-service-specification#delete-host).

## Visibility of Client ID

As specified in [RFC:5731][RFC5731], [RFC:5732][RFC:5732] and [RFC:5733][RFC:5733] the info commands all display a reference  sponsoring entity.

The same scheme will be implemented in RP and the end-user self-service portal (SB) the data presentation is consistent across portals.

The public facing interface is exprected to present the registrar relation aswell. Meaning that the information on registrar relation will be made available:

- in WHOIS
- on www.dk-hostmaster.dk

<a id="references"></a>
## References

- [DK Hostmaster EPP Service Specification][DKHMEPPSPEC]
- [DK Hostmaster EPP Service XSD Repository][DKHMXSDSPEC]
- [RFC:5730 "Extensible Provisioning Protocol (EPP)"][RFC5730]
- [RFC:5731 "Extensible Provisioning Protocol (EPP) Domain Name Mapping"][RFC5731]
- [RFC:5732 "Extensible Provisioning Protocol (EPP) Host Mapping"][RFC5732]
- [RFC:5733 "Extensible Provisioning Protocol (EPP) Contact Mapping"][RFC5733]
- ["DKHM RFC for handling of Automatic Renewal][DKHMRFCAUTORENEW]
- ["DKHM RFC for Transfer Domain EPP Command][DKHMRFCTRANSFER]

[RFC5730]: https://www.rfc-editor.org/rfc/rfc5730.html
[RFC5731]: https://www.rfc-editor.org/rfc/rfc5731.html
[RFC5732]: https://www.rfc-editor.org/rfc/rfc5732.html
[RFC5733]: https://www.rfc-editor.org/rfc/rfc5733.html
[DKHMRFCAUTORENEW]: https://github.com/DK-Hostmaster/DKHM-RFC-AutoRenew
[DKHMRFCTRANSFER]: https://github.com/DK-Hostmaster/DKHM-RFC-Transfer
[DKHMXSD3.2]: https://github.com/DK-Hostmaster/epp-xsd-files/blob/master/dkhm-3.2.xsd
[DKHMEPPSPEC]: https://github.com/DK-Hostmaster/epp-service-specification
[DKHMXSDSPEC]: https://github.com/DK-Hostmaster/epp-xsd-files
[CONCEPT]: https://www.dk-hostmaster.dk/en/new-basis-collaboration-between-registrars-and-dk-hostmaster
