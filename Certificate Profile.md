# Certificate Profile

14 May 2021

Corda Network Certificate Profile 1.0.


## Purpose

This document provides the reference for the Certificate Profile, as a component part of the Certificate Policy for the Corda Network. 

## Certificate Hierarchy

The diagram below illustrates the certificate hierarchy of the Corda Network. The PKI (Public Key Infrastructure) is separated into two components:

* Corda Network certificates which are issued and operated by the Corda Network Foundation (currently R3).
* Corda Node certificates which are issued by the node and controlled by the Node Operator.

## Certificate Cipher Suite and Algorithms

The table below lists the cipher suite and algorithms required by each certificate in the Corda certificate hierarchy.


| **Certificate**          	| **Cipher Suite**   	| **Signature Hash** 	| **Parameters** 	| **Lifetime** 	| **Notes**                                  	|
|--------------------------	|--------------------	|--------------------	|----------------	|--------------	|--------------------------------------------	|
| Corda Foundation Root CA 	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years     	| Permanently offline                        	|
| Subordinate (Issuing) CA 	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years     	| Online subordinate root                    	|
| Doorman CA               	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years     	| Network CA (Issues Node certificates)      	|
| Network Map              	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years     	| Signs the Network Map & Network Parameters 	|
| Service Identity         	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years     	| Notary Signing Key                         	|
| Node CA                  	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years     	| Must contain Name Constraint extension     	|
| Legal Identity           	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years*     |                                            	|
| Node TLS                 	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| 20 years*    	| Expiration defined by node operator        	|
| Confidential Identity    	| ECDSA with SHA-256 	| SHA-256            	| ECDSA_P256     	| -            	| Deprecated                                 	|


*Node Legal Identity and TLS certificates are issued by the Node CA. The lifespan of these certificates is defined by the node operator, but by default will have the same lifespan as the issuing NodeCA.*

## Certificate Profiles

The following section provides the certificate profiles of all certificates in the Corda Network.


| **Extension**       	| **Status**  	| **Constraints**                                                                                                                                                                                                        	|
|---------------------	|-------------	|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| basicConstraints    	| CRITICAL    	| (1) This extension MUST appear as a CRITICAL extension. (2) The CA field MUST be set TRUE. (3) the pathLenConstraint field SHOULD NOT be present.                                                                               |
| keyUsage            	| CRITICAL    	| (1) This extension MUST appear as a CRITICAL extension. (2) Bit positions for keyCertSign and cRLSign MUST be set. (3) if the Root CA Private key is used for signing OCSP responses, then the digitalSignature bit MUST be set.|
| certificatePolicies 	| NOT PRESENT 	| This extension SHOULD NOT be present.                                                                                                                                                                                   	|
| extendedKeyUsage    	| NOT PRESENT 	| This extension MUST NOT be present.                                                                                                                                                                                     	|


## Subordinate CA Certificate


| **Field**                  	| **Status** 	| **Constraints**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        	|
|----------------------------	|------------	|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| certificatePolicies        	| REQUIRED   	| (1)This extension MUST be present and SHOULD NOT be marked as CRITICAL. (2) REQUIRED certificatePolicies: policyIdentifier.  (3) OPTIONAL certificatePolicies: policyQualifiers: policyQualifierID.  (4) OPTIONAL certificatePolicies: policyQualifiers: qualifier: cPSuri.                                                                                                                                                                                                                                                             	|
| cRLDistributionPoints      	| REQUIRED   	| (1) This extension MUST be present and SHOULD NOT be marked as CRITICAL.  (2) It MUST contain the HTTP URL of the CA's CRL service                                                                                                                                                                                                                                                                                                                                                                                                     	|
| authorityInformationAccess 	| REQUIRED   	| (1) With the exception of stapling, which is noted below, this extension MUST be present.  (2) It MUST NOT be marked as CRITICAL.  (3) It MUST contain the URL of the issuing CA's OCSP responder (accessMethod = 1.3.6.1.5.5.7.48.1).  (4) It SGOULD also contain the HTTP URL of the Issuing CA's certificate (accessMethod = 1.3.6.1.5.5.7.48.2).  (5) The HTTP URL of the Issuing CA's OCSP responder MAY be omitted, provided that the Subscriber "staples" the OCSP response for the Certificate in its TLS handshakes [RFC4366] 	|
| basicConstraints           	| CRITICAL   	| (1) This extension MUST appear as a CRITICAL extension.  (2) The CA field MUST be set true.  (3) The pathLenConstraint field SHOULD NOT be present.                                                                                                                                                                                                                                                                                                                                                                                    	|
| keyUsage                   	| CRITICAL   	| (1) This extension MUST be present and MUST be marked CRITICAL.  (2) Bit positions for keyCertSign and cRLSign MUST be set.  (3) If Issuing CA Private Key is used for signing OCSP responses, then the digitalSignature bit MUST be set.                                                                                                                                                                                                                                                                                              	|
| nameConstraints            	| OPTIONAL   	| If present, this extension SHOULD be marked CRITICAL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   	|
| extkeyUsage                	| OPTIONAL   	| For Subordinate CA Certificates to be technically constrained in line with section 7.1.5, then either the value id-kp-serverAuth [RFC5280] or id-kp-clientAuth [RFC5280] or both values MUST be present.                                                                                                                                                                                                                                                                                                                                	|


## All Certificates

All certificates issued under a Corda Subordinate CA must have the following common property:


| **Field**                        	| **Status** 	| **Constraint**                                                                                                                                                            	|
|----------------------------------	|------------	|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| certRole (1.3.6.1.4.1.50530.1.1) 	| REQUIRED   	| (1) This extension MUST be present and SHOULD NOT be marked as CRITICAL. (2) certRole is a custom X.509 extension. It has been registered with OID 1.3.6.1.4.1.50530.1.1. 	|

Corda uses a custom X.509 extension to represent the purpose of each certificate in the Corda PKI. This extension is referred to as the Certificate Role and has an OID of 1.3.6.1.4.1.50530.1.1. The extension contains a single ASN.1 integer which defines the certificate's role.



| **Certificate Role**  	| **Value** 	| **ASN.1 encoding** 	|
|-----------------------	|-----------	|--------------------	|
| Doorman CA            	| 1         	| 02 01 01           	|
| Network Map           	| 2         	| 02 01 02           	|
| Service Identity      	| 3         	| 02 01 03           	|
| Node CA               	| 4         	| 02 01 04           	|
| TLS                   	| 5         	| 02 01 05           	|
| Legal Identity        	| 6         	| 02 01 06           	|
| Confidential Identity 	| 7         	| 02 01 07           	|


## Doorman CA


| **Field**                        	| **Status** 	| **Constraint**                                                                                                                                                                                                                                 	|
|----------------------------------	|------------	|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| basicConstraints                 	| CRITICAL   	| (1) This extension MUST appear as a CRITICAL extension.  (2) This CA field MUST be set true.  (3) The pathLenConstraint field MAY be set to 2.                                                                                                 	|
| keyUsage                         	| CRITICAL   	| (1) This extension MUST be present and MUST be market CRITICAL.  (2) Bit positions for keyCertSign and cRLSign Must be set.   (3) If the Doorman CA Private Key is used for signing OCSP responses, then the digitalSignature bit MUST be set. 	|
| certRole (1.3.6.1.4.1.50530.1.1) 	| REQUIRED   	| (1) This extension MUST be present and SHOULD NOT be marked as CRITICAL.  (2) certRole is a custom extension with OID 1.3.6.1.4.1.50530.1.1.  (3) It SHOULD contain the value 02 01 01 which corresponds to Doorman_CA.                        	|


## Network Map


| **Field**                        	| **Status** 	| **Constraint**                                                                                                                                                                                                           	|
|----------------------------------	|------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| basicConstraints                 	| OPTIONAL   	| If present, the CA field MUST be set to false.                                                                                                                                                                           	|
| keyUsage                         	| CRITICAL   	| (1) This extension MUST be present and MUST be market CRITICAL.  (2) The Bit positions for digitalSignature MUST be set.                                                                                                 	|
| certRole (1.3.6.1.4.1.50530.1.1) 	| REQUIRED   	| (1) This extension MUST be present and SHOULD NOT be marked as CRITICAL.  (2) certRole is a custom extension with OID 1.3.6.1.4.1.50530.1.1.  (3) It SHOULD contain the value 02 01 02 which corresponds to NETWORK_MAP. 	|


## Service Identity


| **Field**                        	| **Status** 	| **Constraint**                                                                                                                                                                                                           	|
|----------------------------------	|------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| basicConstraints                 	| OPTIONAL   	| If present, the CA field MUST be set to false.                                                                                                                                                                           	|
| keyUsage                         	| CRITICAL   	| (1) This extension MUST be present and MUST be market CRITICAL.  (2) The Bit positions for digitalSignature MUST be set.                                                                                                 	|
| certRole (1.3.6.1.4.1.50530.1.1) 	| REQUIRED   	| (1) This extension MUST be present and MUST be marked as CRITICAL.  (2) certRole is a custom extension with OID 1.3.6.1.4.1.50530.1.1.  (3) It SHOULD contain the value 02 01 03 which corresponds to SERVICE_IDENTITY. 	|


## Node CA


| **Field**                        	| **Status** 	| **Constraint**                                                                                                                                                                                                                                                                                             	|
|----------------------------------	|------------	|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| basicConstraints                 	| CRITICAL   	| (1) This extension MUST appear as a CRITICAL extension.  (2) The CA field MUST be set true.  (3) The pathLenConstraint field MAY be set to 1.                                                                                                                                                              	|
| keyUsage                         	| CRITICAL   	| (1) This extension MUST be present and MUST be marked CRITICAL.  (2) Bit positions for keyCertSIgn and cRLSign MUST be set.  (3) If the Node CA Private Key is used for signing OCSP responses, then the digitalSignature bit MUST be set.                                                                 	|
| nameConstraints                  	| CRITICAL   	| (1) This extension MUST be present and MUST be marked CRITICAL.  (2) Permitted subtree MUST be present.  (3) REQUIRED DirectoryName MUST be present and should contain the X.500 distinguished name of the node - it's 'Legal Identity' in the Corda Network.  (4) Excluded subtree SHOULD NOT be present. 	|
| certRole (1.3.6.1.4.1.50530.1.1) 	| REQUIRED   	| (1) This extension MUST be present and SHOULD NOT be marked as CRITICAL.  (2) certRole is a custom extension with OID 1.3.6.1.4.1.50530.1.1.  (3) It SHOULD contain the value 02 01 04 which corresponds to NODE_CA.                                                                                       	|
