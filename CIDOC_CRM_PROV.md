# Combining CIDOC CRM with W3C PROV data

This file contains notes about using W3C PROV with CIDOC CRM for representing the Carolan Guitar's story.  Initially, I look at the overall process description (provenance) options. Later, I look at some more detailed modellimng issues.

## Background

I am looking at creating exploratory Digital Music Objects (DMOs) for the FAST project in a number of specific areas:

* Capturing stories told by the Carolan Guitar (http://carolanguitar.com, http://www.nottingham.ac.uk/ComputerScience/Outreach/Carolan-Guitar.aspx)
* Capturing information about performances (e.g. http://www.birminghamhippodrome.com/WhatsOn_focus.asp?showId=1817, http://demo.annalist.net/annalist/c/DMO_Experiment/d/)
* Capturing information about computational analysis of music (http://etree.linkedmusic.org/about/calma.html, http://demo.annalist.net/annalist/c/CALMA_data/d/)

A common theme that appears in these examples is _provenance_, which captures the relatonships between artifacts, processes and the agents that control or influence the processes; i.e. for anwering quastions along the lines of "how did this artifact come to be", or "what people and objects were involved in that historical event".  There are two standards that are appropriate for capturing this kind of information:

1. CIDOC-CRM (http://www.cidoc-crm.org), an ISO-blessed standard for cultural heritage documentation, and
2. W3C PROV (http://www.w3.org/TR/prov-overview/), a web standard that derives from requirements to capture information about the creation of digital artifacts.

With their different backgrounds, CIDOC CRM and W3C PROV cover a lot of common ground, but extend that ground in different directions.  CIDOC CRM provides a rich and extensible framework for capturing quite subtle details about artifacts, events, people and other aspects of cultural heritage.  By comparison, PROV provides a basic set of concepts that are designed to be combined with other metadata standards to capture information about how an artifact was produced.  A key difference is that the W3C PROV information is intended to represent a definitive set of observations about how an artifact came to exist, usable directly as supporting evidence for some claim, where culural heritage provenance may be a matter of opinion and may legitimately exist in contradictory versions.

On a cursory examination of the use-cases, it appears to me that:

* The Carolan Guitar is a cultural artifact, and recording its story will be facilitated by the relative richness of CIDOC CRM.
* The CALMA data about computational analysis is appropriately recorded with W3C PROV provenance data
* The performance data lies somewhere between: there are clear definitive facts about the performance (who, where, when), but also the performance itself has a cultural element and there may be, for example, views about its curation and reception that are less definitive.

I believe that is important not to allow these different choices to result in provenance "silos" that cannot be connected, so this note is my initial attempt to sketch a way in which both CIDOC CRM and W3C PROV may be used freely, without losing information though use of different terms for common concepts (cf. "you like tomato...", https://www.youtube.com/watch?v=J2oEmPP5dTM - let's *not* call the whole thing off!). In this note, I aim to articulate points of contact between the different standards in a way that can be used to assist coverage of capture (using terms from either) and recovery of information.


## W3C PROV basics

See:
* http://www.w3.org/TR/prov-overview/

The goals of PROV are more modest than CRM, viz to capture details of the mechanisms whereby some artifact or information was derived.  It is built around a very small set of core concepts:

* Entities: anything which can have an associated provenance description,
* Activities: processes that consume and create entities, and
* Agents: people, software and other actors that control or guide the conduct of an activity

![PROV core structure](http://www.w3.org/TR/2013/REC-prov-o-20130430/diagrams/starting-points.svg)

(Diagram from W3C PROV-O document, based on an illustration by Jun Zhao)

There are several additional classes and relations defined by PROV that are mostly refinements of these core concepts, or structural elements used to help organize provenance descriptions.

The following example is a partial description of a Carolan Gitar performance based on information found at [http://carolanguitar.com/](), intended to illustrate the use of PROV vocabulary top describe a performance and production event.  It does not purport to be a complete provenance of either the performance or the production.

Example (using Turtle notation):

    @prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix prov: <http://www.w3.org/ns/prov#> .
    @prefix foaf: <http://xmlns.com/foaf/0.1/> .
    @prefix ex:   <http://vocab.example.org/> .

    ex:Coquette_performance
        a ex:Performance, prov:Activity ;
        prov:Used ex:Carolan_Guitar ;
        prov:wasAssociatedWith ex:Remi_Harris ;
        prov:qualifiedAsscoiation [
            a prov:Association ;
            prov:agent   ex:Remi_Harris ;
            prov:hadRole ex:Performer ;
            prov:hadPlan ex:Coquette_song ;
            ] ;
        rdfs:seeAlso <http://carolanguitar.com/2014/11/10/31-coquette/> ;
        .
    ex:Remi_Harris
        a ex:Guitarist, prov:Person, prov:Agent, foaf:Person ;
        foaf:name "Remi Harris" ;
        rdfs:seeAlso <http://www.remiharris.co.uk> ;
        .
    ex:Coquette_song
        a prov:Plan, prov:Entity ;
        rdfs:label "Coquette" ;
        rdfs:seeAlso <https://en.wikipedia.org/wiki/Coquette_(song)> ;
        .
    ex:Carolan_Guitar
        a ex:Musical_instrument, prov:Entity ;
        prov:wasGeneratedBy ex:Carolan_Guitar_production ;
        rdfs:seeAlso <http://carolanguitar.com> ;
        .
    ex:Carolan_Guitar_production
        a prov:Activity ;
        prov:wasAssociatedWith ex:Nick_Perez, ex:Steve_Benford ;
        prov:used ex:Reclaimed_Mahogany_piece, ex:Flamed_Maple_piece, ex:Spruce_piece ;
        prov:qualifiedAsscoiation [
            a prov:Association ;
            prov:agent   ex:Nick_Perez ;
            prov:hadRole ex:Luthier ;
            prov:hadPlan ex:Dreadnought_Guitar ;
            rdfs:seeAlso <http://carolanguitar.com/2014/08/11/dread/> ;
            ] ;
        .
    ex:Nick_Perez
        a ex:Luthier, ex:Guitarist, prov:Person, prov:Agent, foaf:Person ;
        foaf:name "Nick Perez" ;
        rdfs:seeAlso <http://www.nperezguitars.com> ;
        .
    ex:Steve_Benford
        a ex:Researcher, ex:Guitarist, prov:Person, prov:Agent, foaf:Person ;
        foaf:name "Steve Benford" ;
        rdfs:seeAlso <https://www.nottingham.ac.uk/computerscience/people/steve.benford> ;
        .
    ex:Performer
        a prov:Role ;
        rdfs:label "Player in a music performance" ;
        .
    ex:Luthier
        a prov:Role, rdfs:Class ;
        rdfs:label "Guitar maker" ;
        .
    ex:Researcher
        a prov:Role, rdfs:Class ;
        rdfs:label "Researcher" ;
        .      
    ex:Guitarist
        a prov:Role, rdfs:Class ;
        rdfs:label "Guitarist" ;
        .


![Graphical view](https://raw.githubusercontent.com/gklyne/DMO_Experiment/master/Coquette_prov_example.png)

[(Graphical view in SVG)](https://rawgit.com/gklyne/DMO_Experiment/master/Coquette_prov_example.svg)

Notes:

* `ex:Guitarist` and `ex:Luthier` are used here as both `prov:Role` and `rdfs:Class`.  I think this is OK.


## CIDOC CRM basics

See CRM Core:
* http://www.cidoc-crm.org/docs/paper16.pdf
* http://dl.acm.org/citation.cfm?id=1367085

See also online CRM reference and primer:
* http://erlangen-crm.org/docs/ecrm/current/
* http://erlangen-crm.org/docs/efrbroo/latest/
* http://www.cidoc-crm.org/docs/CRMPrimer_v1.1.pdf

CIDOC CRM data is substantially structured around and mediated by *events*.  Events are, in turn, associated with artifacts, people, places, time spans and more.  Using these concepts, the history of an artifact can be captured through a series of events which contributed to its eventual being, which in turn may refer to other artifacts and people that contributed in various ways to the process.  The same framework can be used to relate an event to the things, people, places and more that contributed to its occurrence, and to further events, people and things that were influenced by that event.

Key high-level concepts in CRM include:

* [crm:E1_Entity](http://erlangen-crm.org/docs/ecrm/current/#anchor-2008326450)
    * [crm:E4_Period](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1061023960)
        * [crm:E5_Event](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-680408142)
            * [crm:E7_Activity](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-121262637)
    * [crm:E39_Actor](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1773481576)
        * [crm:E21_Person](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor2037747319)
    * [crm:E52_Time-Span](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-811589756)
    * [crm:E53_Place](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor696186594)
    * [crm:E70_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1522879172)
        * [crm:E24_Physical_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1321167518)
            * [crm:E22_Man-Made_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-579380173)
        * [crm:E28_Conceptual_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-2048876791)
            * [crm:E73_Information_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1946407753)

(The choice of key concepts here is somewhat arbitrary, and loosely follows the spirit if not the letter of the CRM Core vocabulary element selection.  It is based in part on past experience of terms used to capture descriptions of cultural objects.  The online CRM reference can be used to navigate to related relations and concepts: these terms provide some entry points for exploring the richness of CIDOC CRM.)

<!--
NOTE: in reviewing CIDOC CRM for this note, the most specific concept I can see covering musical performance (as opposed to making a recording) appears to be E7 Activity.  This is supported by [FRBRoo](http://www.cidoc-crm.org/frbr_inro.html), which models `frbr:F31_Performance` as a direct subclass of `crm:E7_Activity`.
-->

Partial rework of previous example using CIDOC CRM and FRBROO extensions:

    @prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix rdfs:   <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix crm:  <http://erlangen-crm.org/current/> .
    @prefix frbroo: <http://erlangen-crm.org/efrbroo/> .
    @prefix foaf:   <http://xmlns.com/foaf/0.1/> .
    @prefix ex:     <http://vocab.example.org/> .

    ex:Coquette_performance
        a crm:E7_Activity, frbroo:F31_Performance ;
        crm:P16_used_specific_object ex:Carolan_Guitar ;
        crm:P14_carried_out_by ex:Remi_Harris ;
        crm:P33_used_specific_technique ex:Coquette_song ;
        frbroo:R25_performed ex:Coquette_song ;
        rdfs:seeAlso <http://carolanguitar.com/2014/11/10/31-coquette/> ;
        .
    ex:Remi_Harris
        a crm:E39_Actor, foaf:Person ;
        crm:P2_has_type ex:Performer, ex:Guitarist ;
        foaf:name "Remi Harris" ;
        rdfs:seeAlso <http://www.remiharris.co.uk> ;
        .
    ex:Coquette_song
        a crm:E29_Design_or_Procedure, frbroo:F25_Performance_Plan ;
        rdfs:label "Coquette" ;
        rdfs:seeAlso <https://en.wikipedia.org/wiki/Coquette_(song)> ;
        .
    ex:Carolan_Guitar
        a crm:E70_Thing, crm:E24_Physical_Man-Made_Thing, crm:E22_Man-Made_Object ;
        crm:P2_has_type ex:Musical_instrument ;
        crm:P108i_was_produced_by ex:Carolan_Guitar_production ;
        rdfs:seeAlso <http://carolanguitar.com> ;
        .
    ex:Carolan_Guitar_production
        a crm:E7_Activity, crm:E12_Production ;
        crm:P14_carried_out_by ex:Nick_Perez, ex:Steve_Benford ;
        crm:P16_used_specific_object
            ex:Reclaimed_Mahogany_piece, ex:Flamed_Maple_piece, ex:Spruce_piece ;
        crm:P126_employed
            ex:Reclaimed_Mahogany, ex:Flamed_Maple, ex:Spruce ;
        crm:P33_used_specific_technique ex:Dreadnought_Guitar ;
        crm:P14_carried_out_by ex:Nick_Perez ;
        frbroo:R28_produced ex:Carolan_Guitar ;
        rdfs:seeAlso <http://carolanguitar.com/2014/08/11/dread/> ;
        .
    ex:Nick_Perez
        a crm:E39_Actor, foaf:Person ;
        crm:P2_has_type  ex:Luthier, ex:Guitarist ;
        foaf:name "Nick Perez" ;
        rdfs:seeAlso <http://www.nperezguitars.com> ;
        .
    ex:Steve_Benford
        a crm:E39_Actor, foaf:Person ;
        crm:P2_has_type  ex:Researcher, ex:Guitarist ;
        foaf:name "Steve Benford" ;
        rdfs:seeAlso <https://www.nottingham.ac.uk/computerscience/people/steve.benford> ;
        .
    ex:Musical_instrument
        a crm:E55_type ;
        rdfs:label "A musical instrument" ;
        .   
    ex:Performer
        a crm:E55_type ;
        rdfs:label "Player in a music performance" ;
        .
    ex:Luthier
        a crm:E55_type, rdfs:Class ;
        rdfs:label "Guitar maker" ;
        .
    ex:Researcher
        a crm:E55_type, rdfs:Class ;
        rdfs:label "Researcher" ;
        .      
    ex:Guitarist
        a crm:E55_type, rdfs:Class ;
        rdfs:label "Guitarist" ;
        .
    ex:Reclaimed_Mahogany_piece
        a crm:E18_Physical_Thing ;
        crm:P45_consists_of ex:Reclaimed_Mahogany ;
        .
    ex:Flamed_Maple_piece
        a crm:E18_Physical_Thing ;
        crm:P45_consists_of ex:Flamed_Maple ;
        .
    ex:Spruce_piece
        a crm:E18_Physical_Thing ;
        crm:P45_consists_of ex:Spruce ;
        .
    ex:Reclaimed_Mahogany
        a crm:E57_Material
        .
    ex:Flamed_Maple
        a crm:E57_Material
        .
    ex:Spruce
        a crm:E57_Material
        .


This example also makes use of a CIDOC CRM extension FRBRoo, which is a reconfiguration of the FRBR (Functional Requirements for Bibliographic Records) vocabulary around the CIDOC CRM event model.  The modelling here introduces `crm:E55_type` values rather than new RDF classes for locally defined types, but either way should be workable.

This example raises two surprising observations:

1. On its own, CIDOC CRM has no terms that capture the notion of a performance more precisely than some kind of activity.  [FRBRoo](http://www.cidoc-crm.org/frbr_inro.html) provides some extensions to the CRM, including a definition of `frbroo:F31_Performance` as a direct subclass of `crm:E7_Activity`.

2. Compared with PROV, CIDOC CRM is very weak at describing details of production activities and performances, even when augmented with the FRBRoo terms.  There appears to be no single established way to associate an agent with a role in an activity (though there is some discussion in [How to model Roles in the CIDOC‐CRM RDF encoding](http://www.ics.forth.gr/isl/CRMext/Roles.pdf)).

On the other hand, CIDOC CRM does seem to provide a more precise framework for describing the creation of artifacts.  PROV, by comparison, focuses very much on how artifacts are used and generated, and relies on extrernally defined terms to refine the nature of those artifacts.  (There is work related to PROV to describe potential or planned activities in more general terms; e.g. [P-PLAN](http://vocab.linkeddata.es/p-plan/), [WfDesc](http://wf4ever.github.io/ro/#wfdesc), [OreChem](http://eprints.soton.ac.uk/179619/1/05693933.pdf), which might provide some of this framework.)


## Using CIDOC CRM alongside PROV

### Classes

PROV class      | CIDOC CRM class       | Comment
----------      | ---------------       | -------
`prov:Entity`   | `crm:E70_Thing`     | Common sublasses used might be `crm:E22_Man-Made_Object` and `crm:E73_Information_Object`
`prov:Activity` | `crm:E5_Event`      | `crm:E7_Activity` is subclass that further constrains to "actions intentionally carried out by instances of E39 Actor", which I would judge the appropriate mapping for a musical performance
`prov:Agent`    | `crm:E39_Actor`     | The CIDOC CRM term here is specifically intended to refer to people, individuall or collectively.  I am not seeing an obvious candidate for software agents, which are also covered by the term `prov:Agent`:  `crm:E29_Design_or_Procedure` might be applicable for this, but is discouraged by the qualification "in particular [...] deliberate human activities that may result in the modification or production of instances of E24 Physical Thing".  The term `crm:E73_Information_Object` could apply, but is not so specific.
`prov:Role`     | `crm:E55_type`      | The CIDOC CRM term here is much less precise, in that it applies generally to a `crm:E1_CRM_Entity` (via `crm:P2_has_type`) rather than more specifically to an agent, and is not scoped to a particular activity involving the agent.


Note, CIDOC CRM defines a number of intervening casses in the overall hierarchy:

* [crm:E1_Entity](http://erlangen-crm.org/docs/ecrm/current/#anchor-2008326450)
    * [crm:E2_Temporal_Entity](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1417799787)
        * [crm:E4_Period](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1061023960)
            * [crm:E5_Event](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-680408142)
                * [crm:E7_Activity](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-121262637)
                    * [crm:E12_Production](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-432649927)
                    * _and several other refinements of activity_
    * [crm:E52_Time-Span](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-811589756)
    * [crm:E53_Place](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor696186594)
    * [crm:E77_Persistent_Item](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1004077320)
        * [crm:E70_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1522879172)
            * [crm:E71_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1603840578)
                * [crm:E28_Conceptual_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-2048876791)
                    * [crm:E90_Symbolic_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-395324192), 
                      [crm:E89_Propositional_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1515448893)
                        * [crm:E73_Information_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1946407753)
                            * [crm:E29_Design_or_Procedure](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1838075452)
                            * [crm:E31_Document](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1608334786)
                * [crm:E24_Physical_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1321167518)
                    * [crm:E22_Man-Made_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-579380173)
        * [crm:E39_Actor](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1773481576)
            * [crm:E21_Person](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor2037747319)
            * [crm:E74_Group](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor766224729)


### Properties

Properties describe relations between designated types of entities.  Complex paths are presented using a notation based on SPARQL property paths (http://www.w3.org/TR/sparql11-query/#propertypaths)

PROV property               | CIDOC CRM encoding        | Comment
-------------               | ------------------        | -------
**Properties of `prov:Entity`** | | 
`prov:wasDerivedFrom`       | `crm:P108i_was_produced_by` / `crm:P16_used_specific_object`  | Entity-to-entity derivation
`prov:wasGeneratedBy`       | `crm:P108i_was_produced_by`                                   |
`prov:wasAttributedTo`      | `crm:P108i_was_produced_by` / `prov:P14_carried_out_by`       | Entity-to-agent attribution
**Properties of `prov:Activity`** | | 
`prov:used`                 | `crm:P16_used_specific_object`                                | CIDOC CRM also provides term `crm:P126_employed` which appears similar, but note that this indicates not a specific entity but rather a kind of material, of which a specific entity might be comprised
`prov:wasInformedBy`        | `crm:P16_used_specific_object` / `crm:P108i_was_produced_by`  | Activity-to-activity propagation (cf. `prov:wasDevivedFrom`)
`prov:startedAtTime`        | `crm:P4_has_time-span`                                        | The `crm:E52_Time-Span` time-span encompases start and end times, and the methods for specificating it are quite open.  In previous implementation work, I've introduced additional properties of `crm:E52_Time-Span` taking ISO8601/RFC3999 literal values to define a period specifically, which can be used alongside (say) `crm:P78_is_identified_by`
`prov:endedAtTime`          | `crm:P4_has_time-span`                                        | (see `prov:startedAtTime`)
`prov:wasAssociatedWith`    | `crm:P14_carried_out_by`                                     |
**Properties of `prov:Agent`** | | 
`prov:ActedOnBehalfOf`      | | Agent-to-agent delegation of responsibility.  I've not yet identified an easy way to capture this in CIDOC CRM.  I imagine one could create a delegation event that captures the delegation of responsibility with respect to some other designated activity.  Overall, CIDOC CRM seems to be weak on the representation of agency other than directly by a person of group of people.
**Other properties** | | 
`prov:hadRole`              | | Agent role in specific activity.  This is not directly captured in CIDOC CRM (as presented in the Erlangen OWL implementation).  The nearest approach seems to be to use `crm:P2_has_type` applied to the agent entity, but that is less precise as it applies generally to the agent rather than to their role in a particular activity.  See also [How to model Roles in the CIDOC‐CRM RDF encoding](http://www.ics.forth.gr/isl/CRMext/Roles.pdf).


### Inferences

@@Add notes about inferences to make information available

@@assume: subclass closure, subproperty closure

@@implementation options: materialize inferred triples (forward chaining), logic programming/query rewriting (backward chaining)


## Tentative conclusions

For describing musical performances, where the roles of players, composers and others are key items of information, the PROV model seems more suitable than CIDOC CRM.

For describing production events like the creation of the Carolan Guitar, CIDOC CRM seems to have some advantages, but again its weakness seems to be in describing the roles of the people involkved (who designed it? Who did various aspects of construction?).  In the case of the Carolan Guitar, a project in an academic setting with contributions by many people, the ability to acknowledge those contributors seems to be of central importance, so the PROV model again seems better suited.  It may be that, via the `prov:Entity >= crm:E70_Thing` or `prov:Entity >= crm:E22_Man-Made_Object` crosswalk, it may be possible to incorporate some of the more precise CIDOC CRM terms applicable to artifact production.


## Artifacts and materials

Having made the decision to use W3C PROV for the basic process modelling structure, an issue arises with the use of materials in a construction process.

PROV does not directly provide for use of an unspecified sample of some material as opposed to a specific sample.  If we attempt to use a generic material type, e.g. "Spruce", as the object of a `prov:Used` statrement, thus:

    ex:Guitar prov:wasGeneratedBy _:MakeGuitar .
    
    _:MakeGuitar a prov:Activity ; 
        prov:used ex:Spruce .

we can run into a modelling problem when we look at more than one construction activity (using an abbreviated form of the above two statements; see [Nesting Unlabeled Blank Nodes in Turtle](http://www.w3.org/TR/turtle/#unlabeled-bnodes)):

    ex:Guitar_1 prov:wasGeneratedBy 
        [ a prov:Activity ; prov:used ex:Spruce ] .
        
    ex:Guitar_2
        [ a prov:Activity ; prov:used ex:Spruce ] .

In this example, what we are actually saying (according to the semantics of PROV and RDF) is that both of `ex:Guitar_1` and `ex:Guitar_2` were made using the *same piece* of Spruce.  This problem can be avoided by introducing an RDF blank node to represent the piece of spruce, and using the term `ex:Spruce` to denote the generic material rather than a specific entity:

    ex:Guitar prov:wasGeneratedBy
      [ a prov:Activity ;
        prov:used
          [ a prov:Entity ;
            ex:consistsOf ex:Spruce 
          ]
      ] .

CRM overcomes this by having separate properties for referencing specific entities and generic materials, which can be seen in the `Carolan_Guitar_production` example above, the salient parts of which are:

    ex:Carolan_Guitar_production
        a crm:E7_Activity, crm:E12_Production ;
        crm:P16_used_specific_object
            ex:Reclaimed_Mahogany_piece, ex:Flamed_Maple_piece, ex:Spruce_piece ;
        crm:P126_employed
            ex:Reclaimed_Mahogany, ex:Flamed_Maple, ex:Spruce ;

This suggests a possible use of the CRM term `crm:P126_employed` to be understood as equivalent to introducing an RDF blank node, thus:

    ex:Carolan_Guitar_production
        a prov:Activity ;
        crm:P126_employed ex:Spruce .

would be equivalent to:

    ex:Carolan_Guitar_production
        a prov:Activity ;
        prov:used
          [ a prov:Entity ;
            crm:P45_consists_of ex:Spruce
          ] .

Which, consistent with the table above, would convey the CRM usage:

    ex:Carolan_Guitar_production
        a crm:E7_Activity, crm:E12_Production ;
        crm:P16_used_specific_object
          [ a crm:E18_Physical_Thing ;
            crm:P45_consists_of ex:Spruce
          ] .

We can now extend the table above to include:

PROV property               | CIDOC CRM encoding        | Comment
-------------               | ------------------        | -------
**Properties of `prov:Activity`** | | 
`prov:used` / `crm:P45_consists_of` | `crm:P126_employed` | Using `crm:P126_employed` avoids having to explicitly mention a blank node or named entity;  rather, the existence of some such entity is implied by the semantics.

Also note the class `crm:E57_Material` which is part of the following CRM class hierarchy:

* [crm:E1_CRM_Entity](http://erlangen-crm.org/docs/ecrm/current/#anchor-2008326450)
  * [crm:E77_Persistent_Item](http://erlangen-crm.org/docs/ecrm/current/#anchor-1004077320)
    * [crm:E70_Thing](http://erlangen-crm.org/docs/ecrm/current/#anchor1522879172)
      * [crm:E71_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/#anchor-1603840578)
        * [crm:E28_Conceptual_Object](http://erlangen-crm.org/docs/ecrm/current/#anchor-2048876791)
          * [crm:E55_Type](http://erlangen-crm.org/docs/ecrm/current/#anchor-1859814565)
            * [crm:E57_Material](http://erlangen-crm.org/docs/ecrm/current/#anchor1645961862)

Note that the "discrete, identifiable, instance" here associated with `crm:E70_Thing` is not a physical object but the concept of the material concerned.  As such it is distinct from `crm:E24_Physical_Man-Made_Thing` of which the guitar is an instance, and from `crm:E18_Physical_Thing` of which any physical sample of the material is an instance (cf. [crm:P45_consists_of](http://erlangen-crm.org/docs/ecrm/current/#anchor-2135682683)).

## NOTES

### FRBRoo terms

* [Combined FRBRoo/CRM browser](http://erlangen-crm.org/docs/efrbroo/120131/)
* [OWLDoc for FRBRoo](http://erlangen-crm.org/docs/efrbroo/120131/ontologies/120131___1327187983.html) and/or [erlangen-crm.org/docs/efrbroo/latest/](http://erlangen-crm.org/docs/efrbroo/latest/)
* [OWLDoc for CIDOC CRM](http://erlangen-crm.org/docs/efrbroo/120131/ontologies/120111___1747105019.html) imported by OWLDoc FRBRoo.
* [F31 performance](http://erlangen-crm.org/docs/efrbroo/120131/classes/F31Performance___1836504452.html) is subclass of E7 Activity.  (Ack. Terhi Nurmikko-Fuller for spotting this.)


### Erlangen bugs/oddities noted:

Erlangen university have created an OWL version of CIDOC CRM, with online hyperlinked documentation.  See [http://erlangen-crm.org/docs/ecrm/current/]().

* P108 (URI has 'i', name does not)  (The CRM spec used to use different property names, e.g. P108_... and P108I_...)
* E29 (typo after ISO)
* Term hyperlinks appear to be auto-generated rather than based on the CRM term, which leads to a possibility that the hyperlinks may not be stable across different versons of the spec.
* [P45_consists_of](http://erlangen-crm.org/docs/ecrm/current/#anchor-2135682683) and its inverse do not follow the convention of adding an "i" after the property number that is used by other property inverses.  (Later) the convention appears to be followed in the URIs, but not in the property names in the text or hyperlinks.


### Validating and graphing RDF data

Online services:

* http://rdf-translator.appspot.com
* http://rhizomik.net/html/redefer/rdf2svg-form/

Using locally installed Redland and Graphviz software:

* rapper -i turtle -o dot Coquette_prov_example.ttl | dot -Tpng -oCoquette_prov_example.png
* rapper -i turtle -o dot Coquette_prov_example.ttl >Coquette_prov_example.dot
* dot -Tpng -oCoquette_prov_example.png Coquette_prov_example.dot
* dot -Tsvg -oCoquette_prov_example.svg Coquette_prov_example.dot
* dot -Tpng -Gsize=24,12 -Gratio=compress -Ecolor=gray -Nwidth=3 -Nheight=0.75 -Nfixedsize=true -oCoquette_prov_example.png Coquette_prov_example.dot
* dot -Tsvg -Gsize=24,12 -Gratio=compress -Ecolor=gray -Nwidth=3 -Nheight=0.75 -Nfixedsize=true -oCoquette_prov_example.svg Coquette_prov_example.dot


