---
title: Media Type Specifications and Registration Procedures
abbrev: Media Type Registration
docname: draft-ietf-mediaman-6838bis-latest
date:
category: bcp
obsoletes: [6838, 9694]

ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
smart_quotes: no
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline]

venue:
  home: "https://datatracker.ietf.org/wg/mediaman/about/"
  repo: "https://github.com/ietf-wg-mediaman/6838bis/"

github-issue-label: 6838bis

author:
 -
    ins: M. Nottingham
    name: Mark Nottingham
    organization: Cloudflare
    postal:
      - Prahran
    country: Australia
    email: mnot@mnot.net
    uri: https://www.mnot.net/
 -
    ins: P. Resnick
    name: Pete Resnick
    organization:
    email: resnick@episteme.net

informative:
  MacOSUTIs:
    title: "Framework: Uniform Type Identifiers"
    target: https://developer.apple.com/documentation/uniformtypeidentifiers
    author:
     -
       org: Apple Computer, Inc.
    date: March 2024

  windowsClipboardNames:
    title: "Clipboard Formats"
    target: https://learn.microsoft.com/en-us/windows/win32/dataxchg/clipboard-formats
    author:
     -
       org: MicroSoft Inc.
    date: August 2020

--- abstract

This document defines procedures for the specification and registration of media types for use in HTTP, MIME, and other Internet protocols.

--- middle

# Introduction

Internet application protocols (including but not limited to HTTP {{?RFC9110}} and MIME {{!RFC2045}}) are capable of carrying arbitrary labeled content.

Those labels are known as media types. A media type consists of a top-level type ({{top-level}}) and a subtype ({{subtypes}}), which is further structured into a tree (identified by a prefix). A subtype can also be associated with a structured syntax (identified by suffix). Optionally, a media type can be defined to allow companion data, known as parameters.

{{requirements}} defines the criteria for registering media types. {{procedures}} outlines the procedures used to do so. The location of the media type registry is:

> http://www.iana.org/assignments/media-types/

{{suffix-procedures}} outlines the procedures for managing the registry for structured syntax suffixes. It is located at:

> https://www.iana.org/assignments/media-type-structured-suffix/

## Conventions Used in This Document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in {{!RFC2119}} when they appear in ALL CAPS. They may also appear in lower or mixed case as plain English words, without any normative meaning.

This specification makes use of the Augmented Backus-Naur Form (ABNF) {{!RFC5234}} notation, including the core rules defined in Appendix B of that document.

# Media Type Registration Requirements {#requirements}

Media type registrations are expected to conform to various requirements laid out in the following sections. Note that specific requirements can vary depending on the registration tree ({{trees}}).

Additional requirements specific to the registration of XML media types are specified in {{!RFC7303}}.

## Functionality

Media types MUST function as actual media formats. Registration of things that are better thought of as a transfer encoding, as a charset, or as a collection of separate entities of another type, is not allowed. For example, although applications exist to decode the base64 transfer encoding {{!RFC2045}}, base64 cannot be registered as a media type.

This requirement applies regardless of the registration tree involved.

## Publication {#publication}

Media types registered in the standards tree by the IETF MUST be published as RFCs. Media types registered in the vendor and personal trees can be published as RFCs, but this is not required.

Standards-tree registrations for media types defined by other standards-related organizations MUST be described by a formal specification produced by that organization.

Other than IETF registrations in the standards tree, the registration of a media type does not imply endorsement, approval, or recommendation by the IANA or the IETF or even certification that the specification is adequate.

Registration of a new top-level type requires Standards Action in the IETF and, hence, the publication of a RFC on the Standards Track.

### Specification Availability

A permanent and readily available public specification of the format for the media type MUST exist for all types registered in the standards tree. This specification needs provide sufficient detail so that interoperability between independent implementations using the media type is possible. If not part of the media type registration proposal, this specification needs to be referenced by it.

A specification need not be publicly available for media types registered in the vendor and personal trees. Note, however, that the public availability of a specification will often make the difference between having a name reserved and having the potential for useful interoperation.

### Intellectual Property

The registration of media types involving patented technology is permitted. However, the restrictions set forth in BCP 79 {{!RFC8179}} and BCP 78 {{!RFC5378}} on the use of patented technology in IETF Standards Track protocols must be respected when the specification of a media type is part of a Standards Track protocol. In addition, other standards-related organizations making use of the standards tree may have their own rules regarding intellectual property that must be observed in their registrations.

Intellectual Property Rights (IPR) disclosures for registrations in the vendor and personal trees are encouraged but not required.

Copyright on the registration template needs to allow the IANA to copy it into the IANA registry.

## Canonicalization and Interoperability

All registered media types MUST employ a single, canonical data format, regardless of registration tree.

Ideally, media types will be defined so they interoperate across as many systems and applications as possible. However, some media types will inevitably have problems interoperating across different platforms. For example, problems with different versions, byte ordering, and specifics of gateway handling can arise.

Universal interoperability of media types is not required, but known interoperability issues should be identified whenever possible. Publication of a media type does not require an exhaustive review of interoperability, and the interoperability considerations section is subject to continuing evaluation.

The recommendations in this subsection apply regardless of the registration tree involved.

## Naming

All registered media types MUST be assigned top-level type and subtype names. The combination of these names serves to uniquely identify the media type, and the subtype name facet (or the absence of one) identifies the registration tree. Both top-level type and subtype names are case-insensitive.

Type and subtype names MUST conform to the following ABNF:

~~~ abnf
  type-name = restricted-name
  subtype-name = restricted-name

  restricted-name = restricted-name-first *126restricted-name-chars
  restricted-name-first  = ALPHA / DIGIT
  restricted-name-chars  = ALPHA / DIGIT / "!" / "#" /
                           "$" / "&" / "-" / "^" / "_"
  restricted-name-chars =/ "." ; Characters before first dot always
                               ; specify a facet name
  restricted-name-chars =/ "+" ; Characters after last plus always
                               ; specify a structured syntax suffix
~~~

Note that this syntax is somewhat more restrictive than what is allowed by {{Section 5.1 of !RFC2045}} or {{Section 4.2 of ?RFC4288}}. Also note that while this syntax allows type and subtype names of up to 127 characters, implementation limits may make such long names problematic. For this reason, 'type-name' and 'subtype-name' SHOULD be limited to 64 characters.

Although this syntax treats "." as equivalent to any other character, characters before any initial "." always specify the registration facet. Note that this means that facet-less standards tree registrations cannot use periods in the subtype name.

Similarly, the final "+" in a subtype name introduces a structured syntax specifier suffix. Structured syntax suffix requirements are specified in {{suffixes}}.

While it is possible for a given media type to be assigned more than one name, the use of different names to identify the same media type is discouraged.

These requirements apply regardless of the registration tree involved.

### Aliases {#deprecated-aliases}

In some cases, a single media type may have been widely deployed using multiple names prior to registration. In such cases, a preferred name MUST be chosen for the media type, and applications are required use this to be compliant with the type's registration. However, a list of deprecated aliases by which the type is known can be supplied as additional information in order to assist applications in processing the media type properly.

## Parameters

Media types can be defined to allow or require use of media type parameters. Additionally, some parameters may be automatically made available to the media type by virtue of being a subtype of a content type that defines a set of parameters applicable to any of its subtypes.

In either case, the names, values, and meanings of any parameters are required to be fully specified when a media type is registered in the standards tree, and should be specified as completely as possible when media types are registered in the vendor or personal trees.

Parameter names have the same syntax as media type names and values:

~~~ abnf
    parameter-name = restricted-name
~~~

Note that this syntax is somewhat more restrictive than what is allowed by the ABNF in {{!RFC2045}} and amended by {{?RFC2231}}.

Parameter names are case-insensitive and no meaning is attached to the order in which they appear. It is an error for a specific parameter to be specified more than once.

There is no defined syntax for parameter values; therefore, it needs to be specified upon registration. Additionally, some transports impose restrictions on parameter value syntax, so care needs be taken to limit the use of potentially problematic syntaxes; for example, binary valued parameters, while permitted in some protocols, are best avoided.

Note that a protocol can impose further restrictions on parameter value syntax, depending on how it chooses to represent parameters. Both MIME {{!RFC2045}} {{?RFC2231}} and HTTP {{!RFC9110}} {{?RFC8187}} allow binary parameters as well as parameter values expressed in a specific charset, but other protocols may be less flexible.

Types already registered in the standards tree should not have new functionality added through the definition of new parameters subsequent to the original registration. New parameters can be used to convey additional information that does not otherwise change existing functionality. An example of this would be a "revision" parameter to indicate a revision level of an external specification such as JPEG. Similar behavior is encouraged for media types registered in the vendor or personal trees, but is not required.

Changes to parameters (including the introduction of new ones) is managed in the same manner as other changes to the media type; see {{change}}.

## Encoding {#encoding}

Some transports impose restrictions on the type of data they can carry. For example, Internet mail traditionally was limited to 7bit US-ASCII text. Encoding schemes are often used to work around such transport limitations.

An "encoding considerations" field is provided to note what sort of data a media type can consist of as part of its registration. Possible values of this field are:

7bit:
: The content of the media type consists solely of CRLF- delimited 7bit US-ASCII text.

8bit:
: The content of the media type consists solely of CRLF- delimited 8bit text.

binary:
: The content consists of an unrestricted sequence of octets.

framed:
: The content consists of a series of frames or packets without internal framing or alignment indicators. Additional out-of-band information is needed to interpret the data properly, including but not limited to knowledge of the boundaries between successive frames and knowledge of the transport mechanism. Note that media types of this sort cannot be stored in a file or transported as a stream of octets without further context; therefore, such media types are thus unsuitable for use in many traditional protocols. A commonly used transport with framed encoding is the Real-time Transport Protocol, RTP. Additional rules for framed encodings defined for transport using RTP are given in {{!RFC4855}}.

Additional restrictions on 7bit and 8bit text are given in {{Section 4.1.1 of !RFC2046}}.

## Fragment Identifiers

Media type registrations can specify how applications should interpret fragment identifiers (specified in {{Section 3.5 of !RFC3986}}) associated with the media type.

Media types are encouraged to adopt fragment identifier schemes that are used with semantically similar media types. In particular, media types that use a structured syntax with a registered "+suffix" MUST follow whatever fragment identifier rules are given in the structured syntax suffix registration.

## Security {#secreq}

All registrations of types in the standards tree MUST include an analysis of security issues. A similar analysis for media types registered in the vendor or personal trees is encouraged but not required.

All descriptions of security issues need to be as accurate as possible regardless of registration tree. In particular, the security considerations MUST NOT state that there are "no security issues associated with this type". Security considerations for types in the vendor or personal tree can say that "the security issues associated with this type have not been assessed".

There is no requirement that media types registered in any tree be secure or completely free from risks. Nevertheless, all known security risks need to be identified in the registration of a media type, again regardless of registration tree.

The security considerations section of all registrations is subject to continuing evaluation and modification, and in particular can be extended by use of the "comments on media types" mechanism described in {{comments}} below.

Issues that need to be described in a security analysis of a media type include:

* Processing of complex media types might institute actions on a recipient's files or other resources. If it is possible to specify arbitrary actions in an unrestricted fashion, it could have devastating effects. See the registration of the application/postscript media type in {{!RFC2046}} for an example of description and handling of these issues.

* Any security analysis MUST state whether or not the format employs such "active content"; if it does, it MUST state what steps have been taken (or are required be taken by applications) of the media type to protect users of the media type.

* Processing of complex media types might institute actions that, while not directly harmful to the recipient, may result in disclosure of information that either facilitates a subsequent attack or else violates a recipient's privacy in some way. Again, the registration of the application/ postscript media type illustrates how such directives can be handled.

* A media type that employs compression may provide an opportunity for sending a small amount of data that, when received and evaluated, expands enormously to consume all of the recipient's resources. All media types should state whether or not they employ compression; if they do, they should discuss what steps need to be taken to avoid such attacks.

* A media type might be targeted for applications that require some sort of security assurance but don't provide the necessary security mechanisms themselves. For example, a media type could be defined for storage of sensitive medical information that in turn requires external confidentiality and integrity protection services, or which is designed for use only within a secure environment. Types should always document whether or not they need such services in their security considerations.


## Additional Information

The following optional information should be included in the specification of a media type if it is available:

* Magic number(s) (length, octet values). Magic numbers are byte sequences that are always present at a given place in the file and thus can be used to identify entities as being of a given media type.

* File name extension(s) commonly used on one or more platforms to indicate that some file contains a given media type.

* macOS Uniform Type Identifier (a string), if it makes sense to exchange media of this type through user-triggered exchange mechanisms such as copy-and-paste or drag-and-drop on macOS and related platforms (see {{MacOSUTIs}} for definitions and syntax).

* Windows clipboard name (a string), if it makes sense to exchange media of this type through user-triggered exchange mechanisms such as copy-and-paste or drag-and-drop on Microsoft Windows and related platforms (see {{windowsClipboardNames}} for definitions and syntax).

In the case of a registration in the standards tree, this additional information can be provided in the formal specification of the media type format. It is suggested that this be done by incorporating the IANA media type registration form into the specification itself.

## Non-Requirements {#non-requirements}

Universal support and implementation of a media type are NOT a requirement for registration.

In some environments such as mail, information on the capabilities of the remote mail agent is frequently not available to the sender. When this is the case, maximum interoperability might be attained by restricting the media types used to those "common" formats expected to be widely implemented.

In the past, this reasoning was used to limit the number of possible media types, and resulted in a registration process with a significant hurdle and delay for those registering media types. However, the need for "common" media types does not require limiting the registration of new media types. If a limited set of media types is recommended for a particular application, that should be asserted by a separate applicability statement specific for that environment.

A media type intended for limited use should note this in its registration. The "Restrictions on Usage" field is provided for this purpose.


# Top-Level Media Types {#top-level}

The list of top-level types is maintained in the IANA Top-Level Media Types registry at:

> https://www.iana.org/assignments/top-level-media-types/

Top-level types can place various restrictions on the media types that use them. New media types MUST conform to the restrictions (if any) of their top-level type.

## Additional Top-Level Types

In some cases, a new media type may not be easily classified under any currently defined top-level type names. Such cases are expected to be quite rare. However, if such a case does arise, a new top-level type can be defined to accommodate it.

### Required Criteria

Definitions of new top-level types are required to fulfil the following criteria:

* The top-level type is defined in a Standards Track RFC (see {{Section 4.9 of !RFC8126}}). This will make sure there is sufficient community interest, review, and consensus.

* The IANA Considerations section of that RFC requests that IANA add this new top-level type to the registry of top-level types.

* The criteria for what types do and do not fall under the new top-level type are defined clearly. This will help expert reviewers to evaluate whether a subtype belongs below the new type or not, and whether the registration template for a subtype contains the appropriate information. If the criteria cannot be defined clearly, this is a strong indication that whatever is being talked about is not suitable as a top-level type.

* The RFC clearly documents security considerations applying to all or a significant subset of subtypes.

* At the minimum, one subtype is described. A top-level type without any subtype serves no purpose. Please note that the 'example' top-level describes a subtype 'example'.

### Additional Considerations

Additional considerations for the definition of a new top-level type include:

* Existing wide use of an unregistered top-level type may be an indication of a need, and therefore an argument for formally defining a new top-level type. On the other hand, the use of unregistered top-level types is highly discouraged.

* Use of an IETF Working Group to define a new top-level type is not needed, but may be advisable in some cases. There are examples of new top-level type definitions without a Working Group ({{?RFC2077}}), with a short, dedicated WG ({{?RFC8081}}), and with a Working Group that included other related work ({{?I-D.ietf-mediaman-haptics}}).

* The document defining the new top-level type should include initial registrations of actual subtypes. The exception may be a top-level type similar to 'example'. This will help to show the need for the new top-level type, will allow checking the appropriateness of the definition of the new top-level type, will avoid separate work for registering an initial slate of subtypes, and will provide examples of what is considered a valid subtype for future subtype registrations.

* The registration and actual use of a certain number of subtypes under the new top-level type should be expected. The existence of a single subtype should not be enough; it should be clear that new similar types may appear in the future. Otherwise, the creation of a new top-level type is likely unjustified.

* The proposers of the new top-level type and the wider community should be willing to commit to emitting and consuming the new top-level type in environments that they control.

* The fact that a group of (potential) types have (mostly) common parameters may be an indication that these belong under a common new top-level type.

* Top-level types can help humans with understanding and debugging. Therefore, evaluating how a new top-level type helps humans understand types may be crucial.

* Common restrictions may apply to all subtypes of a top-level type. Examples are the restriction to CRLF line endings for subtypes of type 'text' (at least in the context of electronic mail), or on subtypes of type 'multipart'.

* Top-level types are also used frequently in dispatching code. For example "multipart/*" is frequently handled as multipart/mixed, without understanding of a specific subtype. The top-level types 'image', 'audio', and 'video' are also often handled generically. Documents with these top-level types can be passed to applications handling a wide variety of image, audio, or video formats. HTML generating applications can select different HTML elements (e.g. \<img> or \<audio>) for including data of different top-level types. Applications can select different icons to represent unknown types in different top-level types.

### Negative Criteria

Negative indicators for creation of a new top-level type include:

* Media types are not a general type system. A top-level type whose main or only purpose is to map other type systems, protocol elements, or registration spaces is not appropriate. Examples of such discouraged uses include mapping media types to programming language primitives, ontologies, object identifiers, URIs and URI schemes, and file extensions. That said, media types can use parameters to carry such information. For example, information on a file extension '.dcat' can be encoded as 'application/octet-string; filename=foo.dcat'.

* A new top-level type should not generate aliases for existing widely used types or subtypes.

* Top-level types with an "X-" prefix cannot be registered, and ought not be used. See {{!RFC6648}}.

# Media Subtypes {#subtypes}

## Registration Trees {#trees}

To increase the efficiency and flexibility of the registration process, different structures of subtype names can be registered in "trees," distinguished with faceted prefixes.

For example, a subtype that is recommended for wide support and implementation by the Internet community would be registered in the standards tree and not have a prefix, while a subtype that is used to move files associated with proprietary software would be registered in the vendor tree, and so its subtype name would begin with a "vnd." prefix.

Note that some previously defined media types do not conform to the naming conventions described below; see {{grandfather}}.

### Standards Tree

The standards tree is intended for those media types that require a substantive review and approval process in a recognized standards-related organization. For media types that do not require such a process, see the vendor and personal trees.

Registrations in the standards tree are either:

1. approved directly by the IESG, or

2. registered by a recognized standards-related organization using the "Specification Required" IANA registration policy {{Section 4.6 of !RFC8126}} (which implies Expert Review), or

3. approved by the Designated Expert(s) as identifying a "community format", as described in {{community}}.

The first procedure is used for registrations from IETF Consensus documents. See {{publication}} for publication requirements.

In the second case, the IESG makes a one-time decision on whether the registration submitter represents a recognized standards-related organization; after that, registration requests are performed as specified in {{review}}. The format is required to be described by a formal specification produced by the submitting standards-related organization.

The third case is described in {{community}}.

Media types in the standards tree do not have faceted subtype names, unless they are grandfathered in using the process described in {{grandfather}}.

The change controller of a media type registered in the standards tree is assumed to be the standards-related organization itself. In the case of IETF standards, the change controller is normally the IESG.

Modification or alteration of the specification uses the same level of processing (e.g., a registration submitted on Standards Track can be revised in another Standards Track RFC, but cannot be revised in an Informational RFC) required for the initial registration.

#### Community Formats in the Standards Tree {#community}

Some formats are interoperable (i.e., they are supported by more than one implementation), but their specifications are not published by a recognized standards-related organization. To accommodate these cases, the Designated Expert(s) are empowered to approve registrations in the standards tree that meet the following criteria:

- There is a well-defined specification for the format
- That specification is not tied to or heavily associated with one implementation
- The specification is freely available at a stable location
- There are multiple interoperable implementations of the specification, or they are likely to emerge
- The requested media type name is appropriate to the use case, and not so generic that it may be considered 'squatting'
- There is no conflict with IETF work or work at other recognised SDOs (present or future)
- There is evidence of broad adoption

The Designated Expert(s) have discretion in applying these criteria; in rare cases, they might judge it best to register an entry that fails one or more.

Note that such registrations still go through preliminary community review ({{preliminary-review}}), and decisions can be appealed ({{review}}).

### Vendor Tree

The vendor tree is intended for media types associated with publicly available products. "Vendor" and "producer" are construed very broadly in this context, and are considered equivalent.

A registration may be placed in the vendor tree by anyone who needs to interchange files associated with some product or set of products. However, the registration properly belongs to the vendor or organization producing the software that employs the type being registered, and that vendor or organization can at any time elect to assume change control of a registration done by a third party in order to correct or update it. See {{change}} for additional information.

When a third party registers a type on behalf of someone else, both entities should be noted in the Change Controller field in the registration. One possible format for this would be "Foo, on behalf of Bar".

Vendor tree registrations are distinguished by the leading facet "vnd.". That may be followed, at the discretion of the registrant, by either a subtype name from a well-known producer (e.g., "vnd.mudpie") or by an IANA-approved designation of the producer's name that is followed by a media type or product designation (e.g., vnd.bigcompany.funnypictures).

While public exposure and review of media types to be registered in the vendor tree are not required, requesting review on the media-types@ietf.org mailing list is encouraged, to improve the quality of those specifications.

Registrations in the vendor tree may be submitted directly to the IANA, where they will undergo Expert Review {{Section 4.5 of !RFC8126}} prior to approval.

### Personal Tree

The personal tree is intended for media types created experimentally or as part of products that are not distributed commercially. This tree is sometimes referred to as the "vanity" tree.

Personal tree registrations are distinguished by the leading facet "prs.".

The change controller of a "personal" registration is the person or entity making the registration, or one to whom responsibility has been transferred as described below.

While public exposure and review of media types to be registered in the personal tree are not required, requesting review on the media-types@ietf.org mailing list is encouraged, to improve the quality of those specifications.

Registrations in the personal tree may be submitted directly to the IANA, where they will undergo Expert Review {{Section 4.5 of !RFC8126}} prior to approval.

### Unregistered x. Tree

Subtype names with "x." as the first facet are intended exclusively for use in private, local environments. Subtypes using this tree cannot be registered and are intended for use only with the active agreement of the parties exchanging them.

The low barrier to registration in the vendor and personal trees means it should rarely, if ever, be necessary to use unregistered types. Therefore, use of types in the "x." tree is strongly discouraged.

Note that types with subtype names beginning with "x-" are no longer considered to be members of this tree (see {{?RFC6648}}). Also note that if a generally useful and widely deployed type incorrectly uses an "x-" subtype name prefix, it can be registered in an alternative tree by following the procedure defined in {{grandfather}}.

### Additional Registration Trees

New top-level registration trees may be created by IETF Standards Action.

It is explicitly assumed that these trees might be created for external registration and management by well-known permanent organizations; for example, scientific societies might register media types specific to the sciences they cover. In general, the quality of review of specifications for one of these additional registration trees is expected to be equivalent to registrations in the standards tree by a recognized standards-related organization.

When the IETF performs such review, it needs to consider the greater expertise of the requesting organization with respect to the subject media type.

## Structured Syntax Suffixes {#suffixes}

Media types can be identified as using a well-known structured syntax (for example, XML or JSON) using use a "+suffix" convention.

A structured syntax suffix is defined as all of the characters to the right of the left-most "+" sign in a media type, including the left-most "+" sign itself. The structured syntax suffix MUST NOT contain more than one "+" sign.

For example, in the "application/foo+bar" media type "application" is the top-level type, "foo" is the subtype name, and "+bar" is the structured syntax suffix. A media type such as "application/foo+bar+baz" is not registrable.

Structured syntax suffixes are required to be registered before use by a media type registration; see {{suffix-procedures}}. Media types that make use of a structured syntax SHOULD use the appropriate suffix, and MUST NOT use suffixes for structured syntaxes that they do not actually employ.

Media types that make use of a structured syntax, or similar separator such as a dash "-", MUST ensure that the registration is semantically aligned, from a data model perspective, with existing subtype names in the media type registry. For example, for the media types "application/foo+bar" and "application/foo+baz", the expectation is that the semantics suggested by the subtype name "application/foo" are the same between both media types. The Designated Expert MUST reject a registration if they believe the semantics for a media type registration does not align with existing subtype names in the media type registry.

Registrants MUST prove to the Designated Expert, such as through an email to a public mailing list or issue tracker comment, that they have consent from the existing change controller for the associated subtype name to register the new media type.

### Use Cases for Structured Syntax Suffixes

Common use cases for media types that employ structured syntax suffixes include:

* Identifying use of a structured data format; for example "+xml", "+json", "+yaml", and "+cbor"
* Flagging compression with a format such as "+zip" or "+gzip"
* Flagging encoding in a digital signature format such as "+jwt" or "+cose"

While it might be desirable to indicate multiple use cases simultaneously using a compound suffix (e.g., "+xml+zip"), experience shows that suffixes are a poor basis for this; the combinations of suffixes quickly multiply, and there is not a well-specified processing model that can handle them safely. Therefore, multiple suffixes are disallowed from use.

### Fragment Identifiers and Structured Syntax Suffixes {#suffix-frag}

Structured syntax suffixes are able to specify fragment identifier handling for all subtypes that utilise them, as indicated in the "Fragment Identifier Considerations" column of the Structured Syntax Suffixes registry.

Individual subtypes can specify additional handling. To ensure consistent processing, precedence is determined by the following rules (first match winning):

1. When the structured syntax suffix defines fragment identifier handling and it successfully resolves the fragment identifier, that determines fragment identifier handling;
2. Otherwise, the specific media type determines fragment identifier handling.

### Security Considerations for Structured Syntax Suffix Processing

Processors that utilise the information in structured syntax suffixes encounter the following security considerations.

#### Relationships Between Types

The relationship between a media type that employs a structured syntax suffix and the type (if any) that results from removing that suffix cannot be known merely by examining the types. For example, content marked "application/foo+bar" may or may not be processable or valid as "application/foo" content. It may be possible to derive one from the other, but that is specific to the structured syntax suffix and/or media type itself.

This uncertainty extends to fragment identifier processing: per the rules in {{suffix-frag}}, a fragment identifier that might be valid for an "application/foo+bar" document might not be applicable to another "+bar" document, because media-type specific fragment identifier resolution might be used.

Likewise, the security characteristics that a processor needs to consider may change depending upon whether it is solely processing the structured syntax suffix or the entire media type. For example, a processor cannot presume that the security characteristics for a "+bar" document will be the same as for a "application/foo+bar" document.

#### Partial Processing

An attacker might append structured syntax suffixes in order to trick processors into skipping security checks. For example, an attacker might use an "application/vnd.ms-excel.addin.macroEnabled.12+zip" structured syntax suffix to trigger an unzip process into invoking Microsoft Excel, bypassing anti-virus scanners that would normally block the file from being opened.

Enterprising attackers might take advantage of toolchains that partially process media types in this manner. Processing of media types based only on the presence of a structured syntax suffix needs to ensure that further processing does not blindly trust the decoded data. For example,  proper magic header or file structure checking could mitigate this attack.

### Additional Structured Syntax Suffixes

The primary guideline for whether a structured syntax suffix is registrable is that it be described by a readily available description, preferably within a document published by an established standards-related organization, and for which there's a reference that can be used in a Normative References section of an RFC.

#  Media Type Registration Procedures {#procedures}

The media type registration procedure is not a formal standards process, but rather an administrative procedure intended to allow community comment and sanity checking without excessive time delay.

Normal IETF processes need to be followed for all IETF registrations in the standards tree. The posting of an Internet Draft is a necessary first step, followed by posting to the media-types@ietf.org list as discussed below.

##  Preliminary Community Review {#preliminary-review}

Notice of a potential media type registration in the standards tree should be sent to the media-types@ietf.org mailing list for review. Registrations in other trees can be sent to the list for review as well; doing so is entirely optional, but is strongly encouraged.

The purpose of this notification is to solicit comments and feedback on the choice of type/subtype name, the unambiguity of the references with respect to versions and external profiling information, and a review of any interoperability or security considerations. The submitter may submit a revised registration proposal or abandon the registration completely and at any time.

## Submit Request to IANA

Media types registered in the standards tree by the IETF itself are reviewed and approved by the IESG as part of the normal standards process.

Standards-tree registrations by recognized standards-related organizations as well as registrations in the vendor and personal trees are submitted directly to the IANA, unless other arrangements were made as part of a liaison agreement.

Registration requests can be sent to iana@iana.org. A web form for registration requests is also available at:

> http://www.iana.org/form/media-types

### Provisional Registrations {#provisional}

Standardization processes often take considerable time to complete. In order to facilitate prototyping and testing, it is often helpful to assign media types early in the process. This way, identifiers used during standards development can remain unchanged once the process is complete, and implementations and documentation do not have to be updated.

Accordingly, provisional registrations of media type names in the standards tree can be submitted to IANA. The only required fields in such registrations are the media type name and contact information (including the standards-related organization name).

Upon receipt of a provisional registration, IANA will check the name and contact information, then publish the registration in a distinct, publicly-visible provisional registration list.

Provisional registrations can be updated or abandoned at any time. When the registration is abandoned, the media type is no longer registered in any sense; it can subsequently be registered just like any other unassigned media type name.

## Review and Approval {#review}

With the exception of provisional standards-tree registrations, registrations submitted to the IANA will be first given to the media types reviewer, who is appointed by the IETF Applications Area Director(s). The media types reviewer examines registration requests to make sure they meet the requirements set forth in this document.

Decisions made by the media types reviewer may be appealed to the IESG using the procedure specified in {{Section 6.5.4 of ?RFC2026}}.

Once a media type registration has passed review, the IANA will register the media type and make the media type registration available to the community.

In the case of standards-tree registrations from other standards-related organizations, IANA will also check that the submitter is in fact a recognized standards-related organization. If the submitter is not currently recognized as such, the IESG will be asked to confirm their status. Recognition from the IESG needs to be obtained before a standards-tree registration can proceed.

## Comments on Media Type Registrations {#comments}

Comments on registered media types may be submitted by members of the community to the IANA at iana@iana.org. These comments will be reviewed by the media types reviewer and then passed on to the change controller of the media type if possible.

Submitters of comments may request that their comment be attached to the media type registration itself; if the IANA, in consultation with the media types reviewer, approves, the comment will be made accessible in conjunction with the type registration.

## Change Procedures {#change}

Once a media type has been published by the IANA, the change controller may request a change to its definition. The same procedure that would be appropriate for the original registration request is used to process a change request.

Media type registrations may not be deleted; media types that are no longer believed appropriate for use can be declared OBSOLETE by a change to their "intended use" field.

Significant changes to a media type's definition should be requested only when there are serious omissions or errors in the published specification. When review is required, a change request may be denied if it renders entities that were valid under the previous definition invalid under the new definition.

The change controller of a media type may pass responsibility to another person or agency by informing the IANA; this can be done without discussion or review.

The IESG may reassign responsibility for a media type. The most common case of this will be to enable changes to be made to types where the author of the registration has died, fallen out of contact, or is otherwise unable to make changes that are important to the community.

## Registration Template

{:vspace}
Type name:
Subtype name:
Required parameters:
Optional parameters:
Encoding considerations:
Security considerations:
Interoperability considerations:
Published specification:
Applications that use this media type:
Fragment identifier considerations:
Additional information:
: Deprecated alias names for this type:

  Magic number(s):

  File extension(s):

  Macintosh file type code(s):
{:vspace}
Person & email address to contact for further information:
Intended usage:
: (One of COMMON, LIMITED USE, or OBSOLETE.)
{:vspace}
Restrictions on usage:
: (Any restrictions on where the media type can be used go here.)
{:vspace}
Author:
Change controller:
Provisional registration? (standards tree only):
: Yes/No

(Any other information that the author deems interesting may be added below this line.)

"N/A", written exactly that way, can be used in any field if desired to emphasize the fact that it does not apply or that the question was not omitted by accident. Do not use 'none' or other words that could be mistaken for a response.

Limited-use media types should also note in the applications list whether or not that list is exhaustive.

# Structured Syntax Suffix Registration Procedures {#suffix-procedures}

Someone wishing to define a "+suffix" name for a structured syntax for use with a new media type registration should:

1. Check IANA's registry of media type name suffixes to see whether or not there is already an entry for that well-defined structured syntax.

2. If there is no entry for their suffix scheme, fill out the template (specified in {{suffix-template}}) and include that with the media type registration. The template may be contained in an Internet Draft, alone or as part of some other protocol specification. The template may also be submitted in some other form (as part of another document or as a stand-alone document), but the contents will be treated as an "IETF Contribution" under the guidelines of BCP 78 {{!RFC5378}}.

3. Send a copy of the template or a pointer to the containing document (with specific reference to the section with the template) to the mailing list media-types@ietf.org, requesting review. This may be combined with a request to review the media type registration. Allow a reasonable time for discussion and comments.

4. Respond to review comments and make revisions to the proposed registration as needed to bring it into line with the guidelines given in this document.

5. Submit the (possibly updated) registration template (or pointer to the document containing it) to IANA at iana@iana.org.

Upon receipt of a structured syntax suffix registration request,

1. IANA checks the submission for completeness; if sections are missing or citations are not correct, IANA rejects the registration request.

2. IANA checks the current registry for an entry with the same name; if such a registry exists, IANA rejects the registration request.

3. IANA requests Expert Review of the registration request against the corresponding guidelines.

4. The Designated Expert may request additional review or discussion, as necessary.

5. If Expert Review recommends registration, IANA adds the registration to the appropriate registry.

The initial registry content specification {{?RFC6839}} provides examples of structured syntax suffix registrations.

## Change Procedures

Registrations may be updated in each registry by the same mechanism as required for an initial registration. In cases where the original definition of the scheme is contained in an IESG-approved document, update of the specification also requires IESG approval.

## Structured Syntax Suffix Registration Template {#suffix-template}

This template describes the fields that must be supplied in a structured syntax suffix registration request:

{:vspace}
Name
: Full name of the well-defined structured syntax.

+suffix
: Suffix used to indicate conformance to the syntax.

References
: Include full citations for all specifications necessary to understand the structured syntax.

Encoding considerations
: A full citation to a section in a specification that provides general guidance regarding encoding considerations for any type employing this syntax. The same requirements for media type encoding considerations given in {{encoding}} apply here.

Interoperability considerations
: A full citation to a section in a specification that documents any issues regarding the interoperable use of types employing this structured syntax should be given here. Examples would include the existence of incompatible versions of the syntax, issues combining certain charsets with the syntax, or incompatibilities with other types or protocols.

Fragment identifier considerations
: A full citation to a section in a specification that documents the generic processing rules of fragment identifiers for any type employing this syntax should be described here.

Security considerations
: A full citation to a section in a specification that provides security considerations shared by media types employing this structured syntax must be specified here. The same requirements for media type security considerations given in {{secreq}} apply here, with the exception that the option of not assessing the security considerations is not available for suffix registrations.

Contact
: Person (including contact information) to contact for further information.

Author/Change controller.
: Person (including contact information) authorized to change this suffix registration.

# Security Considerations

Security requirements for both media type and media type suffix registrations are discussed in {{secreq}}.

# IANA Considerations

## Top-Level Types Registry

In the Top-Level Media Types registry, IANA should link the reference field for each top-level type to the specific subsection in question, rather than just the relevant RFC.


#  Acknowledgments

The current authors would like to acknowledge their debt to the late Dr. Jon Postel, whose general model of IANA registration procedures and specific contributions shaped the predecessors of this document {{?RFC2048}} {{?RFC4288}}. We hope that the current version is one with which he would have agreed but, as it is impossible to verify that agreement, we have regretfully removed his name as a co-author.

Randy Bush, Francis Dupont, Bjoern Hoehrmann, Barry Leiba, Murray Kucherawy, Alexey Melnikov, S. Moonesamy, Mark Nottingham, Tom Petch, Peter Saint-Andre, and Jeni Tennison provided many helpful review comments and suggestions.

--- back

# Historical Note

The media type registration process was initially defined for registering media types for use in the context of the asynchronous Internet mail environment. In this mail environment, there is a need to limit the number of possible media types, to increase the likelihood of interoperability when the capabilities of the remote mail system are not known. As media types are used in new environments in which the proliferation of media types is not a hindrance to interoperability, the original procedure proved excessively restrictive and had to be generalized. This was initially done in {{?RFC2048}}, but the procedure defined there was still part of the MIME document set. The media type specification and registration procedure is now a separate document, to make it clear that it is independent of MIME.

It may be desirable to restrict the use of media types to specific environments or to prohibit their use in other environments. This specification incorporates such restrictions into media type registrations in a systematic way. See {{non-requirements}} for additional discussion.

# Grandfathered Media Types {#grandfather}

Some media types registered prior to 1996 with unfaceted subtype names, would, if registered under the guidelines in this document, be given a faceted name and placed into either the vendor or personal trees. Reregistration of those types to reflect the appropriate trees is encouraged but not required. Ownership and change control principles outlined in this document apply to those types as if they had been registered in those trees.

There may also be cases where a media type with an unfaceted subtype name has been widely deployed without being registered. In these cases, the community format registration process ({{community}}) ought be considered.


