# CRL Profile

## Purpose

This document provides the reference for the Certificate Revocation Profile, as a component part of the Certificate Policy for the Corda Network.

## CRL endpoints

| **Certificate**     	| **CRL endpoint**                     	|
|---------------------	|--------------------------------------	|
| Root                	| none                                 	|
| Subordinate (CNA 1) 	| http://crl.corda.network/cnrc.crl    	|
| Doorman             	| http://crl.corda.network/cna1.crl    	|
| Node CA             	| http://crl.corda.network/doorman.crl 	|
| Node TLS            	| http://crl.corda.network/nodetls.crl 	|

## TLS Revocation Status

Certificate revocation in Corda currently applies to the TLS certificate chain only. Revocation status is validated on peer-to-peer (P2P) connections between nodes. For every P2P connection (for both peers) every certificate in the TLS chain is validated against the corresponding CRL (certificate revocation list) in this order:

1.	Node TLS  

2.	Node CA

3.	Doorman

4.	Subordinate

5.	Root

**Legal Identity Revocation**

Note that the revocation status of Legal Identity and Confidential Identity certificates are enforced in Corda 4.

## CRL Hierarchy

The image below depicts the CRL revocation scheme as defined by the Corda PKI. This is the default configuration that a node will use.

![Corda Network CRL Revocation](/path/to/image.png "CRL Hierarchy")

