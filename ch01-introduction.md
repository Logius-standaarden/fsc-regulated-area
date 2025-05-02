# Introduction

Regulated Area extension is an extension on the Federated Service Connectivity (FSC) standard [FSC Core](https://logius-standaarden.github.io/fsc-core/). 
This extension describes how to create a Regulated Area, how Peers can become a member of a Regulated Area and use the Services within a Regulated Area.
This extension describes a more formal registration process. Specific rules whether a registration is accepted are not part of this extension and left to the rules of the specific FSC Group.

## Purpose

Within an FSC Group there could be additional membership restrictions to either the entire Group or a subsection of this Group called a Regulated Area.
Peers who want to participate in a Regulated Area, publish and consume services within the Regulated Area, must register with this Regulated Area.
The Regulated Area extension introduces a Peer Registration Grant Type as a means for Peers to register with a Membership Administrator and become a member of a Regulated Area. It uses the Properties extension to define the scope of the Regulated Area.

## Terminology

This section lists terms and abbreviations that are used in this document. This document assumes that the reader is familiar with the Terminology of FSC Core.

*Regulated Area*

Part of the FSC Group where only registered members are allowed to publish and connect to Services. The Regulated Area comprises at least one Peer acting as a Membership Administrator and all Peers who are a member of the Regulated Area.
Optionally, a Regulated Area could also have a separate Directories used only within the Regulated Area.

*Peer Registration Grant*

The registration of a Peer in a Regulated Area. The Peer Registration Grant offers a means to perform a registration as well as a basis for a list of Peers of a Regulated Area (the Peers who have a valid Contract with a Peer Registration Grant with the Membership Administrator of the Group are considered part of the Regulated Area).

*Membership Administrator*

A Peer within a Regulated Area who administers membership for this Regulated Area. There can be multiple Membership Administrators within a Regulated Area and one Membership Administrator could be a Membership Administrator for multiple Regulated Areas. 
The Membership Administrator approves and manages membership registrations in the form of Contracts with `Peer Registration Grants` and offers a Membership list for members within the Regulated Area.

## Profiles {#profiles}

When using the Regulated Area extension the following additions **MUST** be made to the [FSC Profile](https://logius-standaarden.github.io/fsc-core/#profiles):
1. The usage of the Properties extension 
2. The usage of the Regulated Area extension
3. Determine the name of the Regulated Area, this is used as an identifier for the Regulated Area, and **MUST** be URL safe
4. Decide on at least one Peer that acts as a Membership administrator

In addition, the mandatory decisions a Profile **MAY** also contain additional agreements or restrictions within the Group. These are not technically required for the operation of FSC Regulated Area extension, but can become mandatory within a Group or Regulated Area. For example an additional set of rules to comply with local legislation.
Below are a few examples listed of these additional decisions, listed for inspirational purposes:
1. entry criteria for members 
2. rules for member Peers to accept contracts of other members
3. validity criteria for members. E.g membership is granted for one year before re-evaluation
4. time period of which Peers that are member of the Regulated area must retrieve the latest revision of the member list. E.g. once every 5 minutes
5. delegatees must also be a member of the Regulated Area
6. restrictions on the duration of the Contract containing the `PeerRegistrationGrant`
7. Appoint one or more Peers who will act as the Directory of the Regulated Area
8. A minimum synchronization interval between Membership Administrators