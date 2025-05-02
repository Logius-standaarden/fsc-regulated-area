# Specifications

## Membership administrator

The Membership administrator is an essential concept within the Regulated Area. The Membership administrator is a Manager chosen by the Group to act as a Membership Administrator for a Regulated Area.
A Regulated Area **COULD** also have multiple Membership Administrators and a Membership Administrator **COULD** also act as a Membership Administrator for multiple Regulated Areas.

A Regulated Area is governed by at least one Membership Administrator. 

The Membership Administrator is responsible for:

* defining the membership criteria for the Regulated Area
* defining the validity period of members in the Regulated Area
* validating Peer Registration Grants of Peers who want to become a member of the Regulated Area
* revoking membership to the Regulated Area of Peers
* providing an interface that returns the Peers with a valid Peer Registration Grant for a Regulated Area

### Multiple Membership Administrators

For Regulated Areas governed by multiple Membership Administrators all members **MUST** be synchronized between Membership Administrators. 
This synchronization allows for easier verification where a Peer verifying an incoming Contract can do so at only one Membership Administrator.

The synchronization interval and error handling **SHOULD** be described in the profile of the Regulated Area.

### Behavior

#### Membership submission

A Peer becomes a Member of a Regulated Area when:
- A valid Contract with a PeerRegistrationGrant exists, signed by one of the Membership Administrators governing the Regulated Area

#### Membership revocation

The Membership Administrator, that has previously accepted the contract with a PeerRegistrationGrant, **MUST** revoke a Contract with a PeerRegistrationGrant to revoke the membership of a Peer.

The Membership Administrator, that has previously accepted the contract with a PeerRegistrationGrant, **MUST** omit the Peer from the response of Peers with a valid Peer Registration Grant.

### Interface

A Membership Administrator **MUST** expose an API with a `GET` `/regulated-areas/{regulated-area-name}/members` endpoint.

The Membership Administrators `GET` `/regulated-areas/{regulated-area-name}/members` endpoint **MUST** use the endpoint and defined in the [OpenAPI Specification](https://gitlab.com/commonground/standards/fsc/-/raw/master/regulated-area/regulated_area_name.yaml)

A Membership Administrator **SHOULD** return all Peers with a valid PeerRegistrationGrant.

A Membership Administrator **SHOULD** return all Peers with a valid PeerRegistrationGrant accepted by the other Membership Administrators in the same Regulated Area.

A Membership Administrator **COULD** allow non-members to make requests to the `GET` `/regulated-areas/{regulated-area-name}/members` endpoint.

The decision for a Membership Administrator to allow non-members to make requests to the `GET` `/regulated-areas/{regulated-area-name}/members` endpoint **MUST** be documented in the rules of the Regulated Area.

## Peer Registration Grant

The content of a PeerRegistrationGrant is defined in the object `.components/schemas/grantPeerRegistration` of the [OpenAPI Specification](https://gitlab.com/commonground/standards/fsc/-/raw/master/manager.yaml)

Validation rules:

- A `PeerRegistrationGrant` cannot be mixed with other Grants. Mixing Grant types with different use-cases is prohibited to prevent the creation of Contracts that are hard to maintain and validate.
- Only one `PeerRegistrationGrant` is allowed per Contract.
- The Peer ID provided by the X.509 certificate used by the Manager of the Membership Administrator matches the value of the field `grant.data.membership_administrator.peer_id`
- The Peer ID provided by the X.509 certificate used by the Manager offering the Contract to the Membership Administrator matches the value of the field `grant.data.member.peer_id`
- The Role of the Peer in the Regulated Area is present in the `grant.data.member.role` field
- The name of the Regulated Area is present in the `grant.data.membership_administrator.regulated_area_name`

Signature requirements:

- A signature is present with the Peer ID of the Peer defined in the field `grant.data.membership_administrator.peer_id`
- A signature is present with the Peer ID of the Peer defined in the field `grant.data.member.peer_id`

### Grant hash

Calculating the Grant hash **MUST** be done according to the specification described in [Core](https://logius-standaarden.github.io/fsc-core/#grant_hash).

The Hash Type mapping for Peer Registration Grant is:

| Hash type                         | int32 value |
|-----------------------------------|-------------|
| HASH_TYPE_PEER_REGISTRATION_GRANT | 6           |

## Rules for validating Contracts {#contract_validation}

Membership Administrator(s) **MUST** define rules for contract validation.

These rules **COULD** include:

- rules for rejecting or accepting Contracts with `ServiceConnectionGrants` and `DelegatedServiceConnectionGrants` 
- rules for accepting or rejecting Contracts with `ServicePublicationGrants` and `DelegatedServicePublicationGrants`
- rules for how to handle delegatees in Contracts with `DelegatedServiceConnectionGrants` and `DelegatedServicePublicationGrants`

## Consuming Peer

### Behavior

#### Membership submission

A Peer who wants to become a member of a Regulated Area **MUST** create a Contract with a [PeerRegistrationGrant](draft-fsc-peer-registration-00-specification.md#peer-registration-grant) 
A Peer who wants to become a member of a Regulated Area **MUST** set the `grant.data.member.role` of the Peer Registration Grant to the value `MEMBER`
A Peer who wants to become a Membership Administrator of a Regulated Area **MUST** set the `grant.data.member.role` of the Peer Registration Grant to the value `MEMBERSHIP_ADMINISTRATOR`

#### Consuming a Service

The validation rules described in this section are additions to the validation Rules described in [FSC Core](https://logius-standaarden.github.io/fsc-core/#service_publication_grant).

A Peer who wants to consume a Service that is part of a Regulated Area needs to create a Contact with a [ServiceConnectionGrant](https://logius-standaarden.github.io/fsc-core/#service_connection_grant) as described in [FSC Core](https://logius-standaarden.github.io/fsc-core/#consuming-a-service).

For consuming a Service that is part of a Regulated Area:
- the `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant` **MUST** contain the address in the form of a [IRI (Internationalized Resource Indicator)](https://datatracker.ietf.org/doc/html/rfc3987) of one of the Membership Administrators of the Regulated Area in the property `membership_administrator_peer_id`.
- the `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant` cannot be mixed with grants containing different `membership_administrator_peer_id`
- the `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant` cannot be mixed with grants that do not contain the property `membership_administrator_peer_id`
- the `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant` **MUST** contain the name of the Regulated Area in the property `regulated_area_name`.
- the `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant` cannot be mixed with grants containing different `regulated_area_name`

## Providing Peer 

### Behavior

#### Validating Contracts

The validation rules described in this section are additions to the validation Rules described in [FSC Core](https://logius-standaarden.github.io/fsc-core/#contracts). 

A Peer receives a request for consuming their Service in the form of a Contract containing a `ServiceConnectionGrant` or a `DelegatedServiceConnectionGrant` **MUST** validate the Contract against the rules defined by the [Membership Administrator](#contract_validation)

If the Service is published as part of a Regulated Area the `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant` it **MUST** contain the address of one of the Membership Administrators of the Regulated Area in a property with the name `membership_administrator_peer_id`.

The membership list **MUST** be obtained by at one of the following methods:
- be obtained by the Membership Administrator for which the Peer receiving the Contract has submitted the Contract with the `PeerRegistrationGrant` for this particular Regulated Area Name and Service.
- be obtained by the Membership Administrator defined in the `membership_administrator_peer_id` property on a Contract with a `ServiceConnectionGrant` or `DelegatedServiceConnectionGrant`.

From a security and trust perspective it is recommended the membership list is obtained from the Membership Administrator for which the Peer receiving the Contract has submitted the Contract with the `PeerRegistrationGrant` for this particular Regulated Area Name and Service.

A Peer offering a Service in the Regulated Area **MUST** Reject all contracts for this Service if the `outway.peer_id` in the `ServiceConnectionGrant` is not on the `Membership list` for the Regulated Area.

A Peer offering a Service in the Regulated Area **MUST** Reject all contracts for this Service if the `outway.peer_id` and `delegator.peer_id` in the `DelegatedServiceConnectionGrant` are not on the `Membership list` for the Regulated Area.

A Peer offering a Service in the Regulated Area **MUST** Reject the Contract if the Contract does not adhere to the rules defined by one of the Membership Administrators.

If the rules defined by the Membership Administrators requires the Peer to verify the members of the Membership Administrator the Peer offering a Service **COULD** use a local copy (cached) version of the `members` response containing the `Membership list`.

#### Publishing a Service

The validation rules described in this section are additions to the validation Rules described in [FSC Core](https://logius-standaarden.github.io/fsc-core/#contracts). 

A Peer who wants to Publish a Service needs to create a Contact with a [ServicePublicationGrant](https://logius-standaarden.github.io/fsc-core/#service_publication_grant) as described in [FSC Core](https://logius-standaarden.github.io/fsc-core/#consuming-a-service) and send it to a [Directory](../core/draft-fsc-core-00-specifications.md#directory-directory) in the Regulated Area.

For publishing a Service that is part of a Regulated Area:
- the `ServicePublicationGrant` or `DelegatedServicePublicationGrant` **MUST** contain the PeerID of one of the Membership Administrators of the Regulated Area in the property `membership_administrator_peer_id`.
- the `ServicePublicationGrant` or `DelegatedServicePublicationGrant` cannot be mixed with grants containing a different `membership_administrator_peer_id`
- the `ServicePublicationGrant` or `DelegatedServicePublicationGrant` cannot be mixed with grants that do not contain the properties `membership_administrator_peer_id`
- the `ServicePublicationGrant` or `DelegatedServicePublicationGrant` **MUST** contain the name of the Regulated Area in the property `regulated_area_name`.
- the `ServicePublicationGrant` or `DelegatedServicePublicationGrant` cannot be mixed with grants containing different `regulated_area_name`

The Peer acting as a Directory for the Regulated Area **MUST** validate the Contract against the rules defined by all the [Membership Administrators](#contract_validation) defined in the `ServicePublicationGrant` or `DelegatedServicePublicationGrant`.

The Peer acting as Directory for the Regulated Area **MUST** Reject the Contract if the Contract does not adhere to the rules defined by all the Membership Administrators.

The Peer acting as Directory for the Regulated Area **MUST** Reject the Contract if the `service.peer_id` is not on the `Members list` for the Regulated Area.

If the rules defined by the Membership Administrators requires the Directory to verify the members of the Membership Administrators the Directory **COULD** use a local copy (cached) version of the `members` response.

#### Issuing Access Tokens

A Peer offering a Service issues Access Tokens to Outways of Consuming Peers as described in [Core](https://logius-standaarden.github.io/fsc-core/#manager_tokens).

Additionally, the following checks **MUST** be performed:
- a Peer offering a Service **MUST** validate that the Peers on the Contract containing the (Delegated) ServiceConnectionGrant are members of the same Regulated Area before issuing an Access Token.
- For Services offered on behalf of another Peer, a delegated Service, the Peer offering the Service (Delegatee) **MUST** verify if the Delegator is still a member of the Regulated Area.

##### Error Responses

If the Peers verified in any of the additional checks mentioned above are not a member of the Regulated Area the `/token` endpoint **MUST** return an error with the following response:
- error: unauthorized_client
- error_description: Peer is not a member of the Regulated Area

## Combining roles 

The roles of Membership administrator and Directory of the Regulated Area **COULD** be combined in one Peer. 