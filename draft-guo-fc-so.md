---

title: "A Profile for Forwarding Commitments (FCs)"
abbrev: "Forwarding Commitments"
category: std

docname: draft-guo-fc-so-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
# keyword:
# - next generation
# - unicorn
# - sparkling distributed ledger
venue:
  #  group: WG
  #  type: Working Group
  #  mail: WG@example.com
  #  arch: https://example.com/WG
  github: "BasilGuo/fc-signed-object"
  latest: "https://BasilGuo.github.io/fc-signed-object/draft-guo-fc-so.html"

author:
  -
      fullname: Ke Xu
      organization: Tsinghua University
      email: xuke@tsinghua.edu.cn
  -
      fullname: Xiaoliang Wang
      orgnaization: Tsinghua University
      email: wangxiaoliang0623@foxmail.com
  -
      fullname: Yangfei Guo
      orgnization: Zhongguancun Labratory
      email: guoyangfei@zgclab.edu.cn
  -
      fullname: Jiangou Zhan
      orgnization: Tsinghua University
      email: "904542587@qq.com" # TODO: use your edu.cn email

normative:
    RFC3779:
    RFC5652:
    RFC6480:
    RFC6485:
    RFC6488:

informative:
    X.680:
      title: "Information technology -- Abstract Syntax Notation One (ASN.1): Specification of basic notation"
      target: "https://itu.int/rec/T-REC-X.680-202102-I/en"
      date: Feb. 2021
      author:
        - ins: ITU-T
      seriesinfo: "Recommendation ITU-T X.680"
    X.690:
      title: "Information technology - ASN.1 encoding rules: Specification of Basic Encoding Rules (BER), Canonical Encoding Rules (CER) and Distinguished Encoding Rules (DER)"
      target: "[https://itu.int/rec/T-REC-X.680-202102-I/en](https://www.itu.int/rec/T-REC-X.690-202102-I/en)"
      date: Feb. 2021
      author:
        - ins: ITU-T
      seriesinfo: "Recommendation ITU-T X.690"


--- abstract

This document defines a standard profile for Forwarding Commitment (FC) used in Resource Public Key Infrastructure (RPKI). A FC is a digitally signed object that provides a means of verifying that an IP address prefix is announced from `AS a` to `AS b`. When validated, a FC's eContent can be used for detection and mitigation of route hijacking and provide protection for the AS_PATH attribute in BGP-UPDATE.


--- middle

# Introduction

TODO: need more revised.
The primary purpose of the Resource Public Key Infrastructure (RPKI) is to improve routing security.  (See {{RFC6480}} for more information.) As part of this system, a mechanism is needed to allow entities to verify that an AS has been given permission by an IP address holder to advertise route along the propagation path. A FC provides this function.

Forwarding Commitment (FC) is a signed object that binds IP prefix with AS and its next hops, eventually it could compose and protect the path of BGP-UPDATE propagation. It uses a Web of Trust in this propagation model. That means It performs more like that originator AS trusts its next hop ASes and sends its route to its next hops. By this means orginator AS has authorized its next hops to propagate its own prefix. And originator AS's next hops would also recive this prefix and send to its next hops. The relationship among them is the signed FC. The Forwarding Commitments also tell the ASes in the propagation path that the previous hop AS has received and selected this AS_PATH.

The FC uses the template for RPKI digitally signed objects {{RFC6488}} for the definition of a Cryptographic Message Syntax (CMS) {{RFC5652}} wrapper for the FC content as well as a generic validation procedure for RPKI signed objects.  As FCs need to be validated with RPKI certificates issued by the current infrastructure, we assume the mandatory-to-implement algorithms in {{RFC6485}}, or its successor.

To complete the specification of the FC (see {{Section 4 of RFC6488}}), this document defines:

1.  The object identifier (OID) that identifies the FC signed object. This OID appears in the eContentType field of the encapContentInfo object as well as the content-type signed attribute within the signerInfo structure).
2.  The ASN.1 syntax for the FC content, which is the payload signed by the BGP speaker. The FC content is encoded using the ASN.1 {{X.680}} Distinguished Encoding Rules (DER) {{X.690}}.
3.  The steps required to validate an FC beyond the validation steps specified in {{RFC6488}}).

## Requirements Language

{::boilerplate bcp14-tagged}


# The FC Content-Type

The content-type for a FC is defined as ForwardingCommitment and has the numerical value of 1.2.840.113549.1.9.16.1.(TBD).

This OID MUST appear both within the eContentType in the encapContentInfo object as well as the content-type signed attribute in the signerInfo object (see {{RFC6488}}).

# The FC eContent

The content of a FC identifies a forwarding commitment and forwarding binding that an AS announces to other nodes upon receiving BGP-UPDATE message. Other ASes which on-path can validate the FC and perform path verification for traffic forwarding based on the AS-path information. Off-path ASes can utilize this FC for collaborative filtering. A FC is an instance of ForwardingCommitmentAttestation, formally defined by the following ASN.1 {{X.680}} module:

RPKI-FC-2023
  { iso(1) member-body(2) us(840) rsadsi(113549) pkcs(1)
     pkcs-9(9) smime(16) modules(0) id-mod-rpki-FC-2022(TBD) }

DEFINITIONS EXPLICIT TAGS ::=
BEGIN

IMPORTS
  CONTENT-TYPE
  FROM CryptographicMessageSyntax-2010 -- RFC 6268
    { iso(1) member-body(2) us(840) rsadsi(113549) pkcs(1)
      pkcs-9(9) smime(16) modules(0) id-mod-cms-2009(58) } ;

id-ct-FC OBJECT IDENTIFIER ::= { iso(1) member-body(2) us(840) rsadsi(113549) pkcs(1)
    pkcs-9(9) id-smime(16) id-ct(1) fc(TDB) }

ct-FC CONTENT-TYPE ::=
    { TYPE ForwardingCommitmentAttestation IDENTIFIED BY id-ct-FC }

ForwardingCommitmentAttestation ::= SEQUENCE {
    version [0]         INTEGER DEFAULT 0,
    originatorASID      ASID,
    prefix              SEQUENCE (SIZE(1)) OF Prefix,
    fc ForwardingCommitment }

ASID ::= INTEGER (0..4294967295)

Prefix ::= SEQUENCE {
    afi                 AFI,
    address             IPAddress,
    prefixLength        INTEGER (SIZE(0..128)}

AFI ::= OCTET STRING (SIZE(2))

IPAddress ::= BIT STRING (SIZE(0..128))

ForwardingCommitment ::= SEQUENCE {
    id                  BIT STRING
    signature           BIT STRING }

END

Note that this content appears as the eContent within the encapContentInfo (see {{RFC6488}}).

## version

The version number of the ForwardingCommitmentAttestation MUST be 0.

## originatorASID

The originatorASID field contains the AS number of the originator AS associated with this FC in the BGP-UPDATE message.

## Type Prefix

Within the Prefix structure, the prefix field encodes the set of IP address prefixes which announced by the originator AS in AS-path.

### Element afi

Within the Prefix structure, afi contains the Address Family Identifier of an IP address family. This specification only supports IPv4 and IPv6. Therefore, afi MUST be either 0001 or 0002. 

### Element address

The address field contains the IP address.

### Element prefixLength

The prefixLength field contains the lenth of IP address prefix.

## Type ForwardingCommitment

Within the ForwardingCommitment structure, the fc field encodes the forwarding commitment which generated by this AS and will be validated by other AS.

### Element id

The id field contains the hash of current AS-path in the associated BGP-UPDATE plus next hop AS and above prefix filed.

### Element signature

Field signature is a signature signs by BGP speaker who issues this FC. We would give an example in {{fc-validation}}.

# FC Validation {#fc-validation}

Before a relying party can use a FC to validate a routing announcement, the relying party MUST first validate the FC. To validate a FC, the relying party MUST perform all the validation checks specified in {{RFC6488}} as well as the following additional FC-specific validation steps.

- The IP Address Delegation extension {{RFC3779}} is present in the end-entity (EE) certificate (contained within the FC), and IP address prefix in the FC payload is contained within the set of IP addresses specified by the EE certificate's IP Address Delegation extension.
- The EE certificate's IP Address Delegation extension MUST NOT contain "inherit" elements described in {{RFC3779}}.
- The Autonomous System Identifier Delegation Extension described in {{RFC3779}} is not used in Forwarding Commitment and MUST NOT be present in the EE certificate.


# Security Considerations

TODO: Security


# IANA Considerations

TODO: An OID for FC is needed.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
