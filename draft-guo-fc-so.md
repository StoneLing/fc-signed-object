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
    email:
-
    fullname: Yangfei Guo
    orgnization: Zhongguancun Labratory
    email: guoyangfei@zgclab.edu.cn
-
    fullname: Jiangou Zhan
    orgnization: Tsinghua University
    email: "904542587@qq.com" # TODO: use your edu.cn email

normative:
    RFC4271
    RFC6480
    RFC6488

informative:


--- abstract

This document defines a standard profile for Forwarding Commitment (FC) objects for use with the Resource Public Key Infrastructure (RPKI). A FC is a digitally signed object that provides a means of verifying that an IP address prefix is announced by `AS a` to `AS b`. When validate, an FC's eContent can be used for detection and mitigation of route hijacking.


--- middle

# Introduction

TODO

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# The FC Content-Type

TODO

# The FC eContent

TBD.

## version

TBD.

## prefixS

TBD.

## prefixD

TBD.

## FC

TBD.

# FC Validation

TBD.



# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
