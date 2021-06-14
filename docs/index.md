# Trust Registry Specification
A trust registry is a mechanism to ensure an issuer is trusted by a recognizable third-party
## Trust Registry Requirements
There is one operation initially proposed here for acceptance as a standard - Check that an issuer is part of a registry.

The registry will be located at a web endpoint accessible by an authorized http GET request. 


The CheckIssuer endpoint is expected to be used by verifiers and holders during a credential exchange right after receiving the verifiable credential and verifying its authenticity. 

Example credential:
```
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "http://example.edu/credentials/3732",
  "type": ["VerifiableCredential", "ProofOfAgeCredential"],
  "issuer": "https://example.edu/issuers/14",
  "registry": "did:web:cloud.trinsic.id:example-registry"
  "issuanceDate": "2010-01-01T19:23:24Z",
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21",
    "ageOver": 21
    },
  "proof": { ..
    "verificationMethod": "did:example:ebfeb1f712ebc6f1c276e12ec21",
     ...
   }
}
```
### Check Registry for Issuer
**Request**

an http get request should be made to the registry endpoint resolved by the did:web: https://cloud.trinsic.id/example-registry.

**Request Model**:
```
{
    "issuer": uri,
    "type": uri
}
```

**Example Request**:
```
{
    "issuer": "https://example.edu/issuers/14", 
    "type": "https://www.w3.org/2018/credentials/examples/v1#ProofOfAgeCredential",
}
```

- `issuer` is the identifier of the issuer. This will be the ID that the issuer uses in the verifiable credentials it issues. It should be either an http URL or a DID.
- `type`is the full type uri of the type that the issuer is authorized to issue. 

Each of these three fields can be found from the verifiable credential. 

**Response**
```
{
    "status": enum,
    "authorization-start-date": date,
    "authorization-end-date": date
}
```
- `status` indicates the status of the issuer. The following enumerations are possible statuses:
    - `current`: the issuer is currently authorized to issue that type
    - `not found`: the combination of issuer & type are not found on the registry
    - `expired`: issuer's authorization was not renewed after the previous valid registration period
    - `terminated`: voluntarily terminated by the issuer - not in scope
    - `revoked`: involuntarily terminated by the governing authority
- `authorization-start-date` is the ISO 8601 datetime string of when the issuer was registered
- `authorization-end-date` is the ISO 8601 datetime string of the issuer's expiration date on the registry
----

**All the below endpoints are merely suggestions for the implementor**
### Register Issuer 

**Request**
```
{
   "issuer": uri
   "type": uri
   "authorization-start-date": iso 8601 date string
   "authorization-end-date": iso 8601 date string
}
```

**Response**
```
 {
    "registry": did:web uri,
    "issuer": uri, 
    "type": uri,
    "capability, uri
}
```

### Revoke Capability
**Request**
```
{
    "registry": uri,
    "entity": uri, 
    "object": uri,
    "capability, uri
}
```

**Response**
````
{
    "status-code": 200
}
```


### A GHP-compliant trust registry:

1. MUST be identified by a trust registry DID generated using a GHP-compliant DID method.
2. MUST support all mandated requirements of the GHP Trust Registry Protocol Specification .
3. SHOULD incorporate throttling, DDOS protection, etc.
4. MAY limit the DID methods permitted to be used for the registered DIDs to a subset of the
GHP-compliant DID methods.

### Trust Registry Protocol
The GHP trust registry protocol:

1. MUST be an open standard royalty-free specification.
2. MUST be specified in either:
2.1. A GHP Trust Registry Protocol Specification.
2.2. A more general purpose trust registry protocol specification if it meets all the
requirements in these recommendations.
3. MUST support trust registry service endpoint resolution using GHP-compliant DID methods.
3.1. SHOULD support the GHP X.509 Subject Alternative Name URI Specification
4. MUST provide a registration method.
5. SHOULD provide a revocation method.
6. SHOULD provide a verifier access request method.
7. MUST support queries consisting of the following parameters to enable issuer authorization:
7.1. Trust registry DID
7.2. Issuer DID
7.3. Verifiable credential type URI
8. MUST support queries of the following parameters for cross-registration of peer trust registries
8.1. Trust registry DID
8.2. Verifiable credential type URI
8.3. Verifiable credential issuance date
9. MUST return exactly one of the following status values:
9.1. Not found
9.2. Current
9.3. Expired (not renewed after the previous valid registration period)
9.4. Terminated (voluntarily terminated by the issuer)
9.5. Revoked (involuntarily terminated by the governing authority)
10. MUST return exactly two date values (formatted to comply with RFC3339, as UTC/Z - with no
offset):
10.1. AuthorizationStartDate - which indicates the data that the Issuer’s authorization started.
10.2. AuthorizationEndDate - which may be null for Issuers that are currently, at time of the
query, an Authorized Issuer. If an Issuer is not currently an Authorized Issuer, the date
that they lost that status will be returned. 

### Issuers
GHP-compliant issuers:

1. MUST be identified by an issuer DID generated using a GHP-compliant DID method.
2. MUST register the issuer DID in the trust registry of any specific EGF under which the issuer
wishes to issue GHP-compliant credentials.
3. MUST issue GHP-compliant verifiable credentials that meet the following requirements:
3.1. The verifiable credential includes a claim specified in the GHP Verifiable Credentials
Specification whose value is the trust registry DID for the specific EGF under which the
credential or pass was issued.
3.2. The value of the verifiable credential issuer ID is the issuer DID registered in the trust
registry identified by the trust registry DID.
3.3. The value of the verifiable credential type is a GHP credential type URI specified in the
GHP EGF. 
### Recommended Timelines
#### Phase One (30 Day Horizon)

GHP-compliant specific governing authorities:

1. SHOULD manually maintain a list of authorized issuers in a DID document using a did:web: URL
as specified GHP Trust Registry Protocol Specification.
1. SHOULD participate in development of the GHP Trust Registry Protocol Specification.
1. SHOULD publish their trust registry development plans.
#### Phase Two (90 Day Horizon)
GHP-compliant specific governing authorities:

1. SHOULD publish their trust registry policies and specifications in their specific EGF.
2. SHOULD have their trust registry implemented.
3. SHOULD pass a GHP-compliant trust registry protocol test suite.
4. SHOULD maintain a list of the trust registry DIDs of other GHP-compliant peer governing
authorities.
#### Phase Three (180 Day Horizon)
GHP-compliant specific governing authorities:
    1. MUST have implemented a GHP-compliant trust registry.
    2. MUST meet all requirements in their specific EGF. 

---
Sources:
1. <a target="_blank" href="assets/GHPC.Interoperability.Blueprint_v0.1.0_May.25.2021.pdf">GHPC Interoperability Blueprint - May 25 2021.pdf</a>
