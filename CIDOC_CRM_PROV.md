# Combining CIDOC CRM with W3C PROV data

## Background

I am looking at creating exploratory DMOs for the FAST project in a number of specific areas:

* Capturing stories told by the Carolan Guitar (http://carolanguitar.com, http://www.nottingham.ac.uk/ComputerScience/Outreach/Carolan-Guitar.aspx)
* Capturing information about performances (e.g. http://www.birminghamhippodrome.com/WhatsOn_focus.asp?showId=1817, http://demo.annalist.net/annalist/c/DMO_Experiment/d/)
* Capturing information about computational analysis of music (http://etree.linkedmusic.org/about/calma.html, http://demo.annalist.net/annalist/c/CALMA_data/d/)

A common theme that appears in these examples is _provenance_, which captures the relatonships between artifacts, processes and the agents that control or influence the processes; i.e. for anwering quastions along the lines of "how did this artifact come to be", or "what people and objects were involved in that historical event".  There are two standards that are appropriate for capturing this kind of information:

1. CIDOC-CRM (http://www.cidoc-crm.org), an ISO-blessed standard for cultural heritage documentation, and
2. W3C PROV (http://www.w3.org/TR/prov-overview/), a web standard that derives from requirements to capture information about the creation of digital artifacts.

With their different backgrounds, CIDOC CRM and W3C PROV cover a lot of common ground, but extend that ground in different directions.  CIDOC CRM provides a rich and extensible framework for capturing quite subtle details about artifacts, events, people and other aspects of cultural heritage.  By comparison, PROV provides a basic set of concepts that are designed to be combined with other metadata standards to capture information about how an artifact was produced.  A key difference is that the W3C PROV information is intended to represent a definitive set of observations about how an artifact came to exist, usable directly as supporting evidence for some claim, where culural heritage provenance may be a matter of opinion and legitimately exist in contradictory versions.

On a cursory examination of the use-cases, it appears to me that:

* The Carolan Guitar is a cultural artifact, and recording its story will be facilitated by the relative richness of CIDOC CRM.
* The CALMA data about computational analysis is appropriately recorded with W3C PROV provenance data
* The performance data lies somewhere between: there are clear definitive facts about the performance (who, where, when), but also the performance itself has a cultural element and there may be, for example, views about its curation and reception that are less definitive.

I believe that is important not to allow these different choices to result in provenance "silos" that cannot be connected, so this note is my initial attempt to sketch a way in which both CIDOC CRM and W3C PROV may be used freely, without losing information though use of different terms for common concepts (cf. "you like tomato...", https://www.youtube.com/watch?v=J2oEmPP5dTM - let's *not* call the whole thing off!). In this note, I aim to articulate points of contact between the different standards in a way that can be used to assist coverage of capture (using terms from either) and recovery of information.


## CIDOC CRM basics

See CRM Core:
* http://www.cidoc-crm.org/docs/paper16.pdf
* http://dl.acm.org/citation.cfm?id=1367085

See also online CRM reference and primer:
* http://erlangen-crm.org/docs/ecrm/current/
* http://www.cidoc-crm.org/docs/CRMPrimer_v1.1.pdf

CIDOC CRM data is substantially structured around and mediated by *events*.  Events are, in turn, associated with artifacts, people, places, time spans and more.  Using these concepts, the history of an artifact can be captured through a series of events which contributed to its eventual being, which in turn may refer to other artifacts and people that contributed in various ways to the process.  The same framework can be used to relate an event to the things, people, places and more that contributed to its occurrence, and to further events, people and things that were influenced by that event.

Key high-level concepts in CRM include:

* [cidoc:E1_Entity](http://erlangen-crm.org/docs/ecrm/current/#anchor-2008326450)
    * [cidoc:E4_Period](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1061023960)
        * [cidoc:E5_Event](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-680408142)
            * [cidoc:E7_Activity](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-121262637)
    * [cidoc:E39_Actor](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1773481576)
        * [cidoc:E21_Person](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor2037747319)
    * [cidoc:E52_Time-Span](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-811589756)
    * [cidoc:E53_Place](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor696186594)
    * [cidoc:E70_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1522879172)
        * [cidoc:E24_Physical_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1321167518)
            * [cidoc:E22_Man-Made_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-579380173)
        * [cidoc:E28_Conceptual_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-2048876791)
            * [cidoc:E73_Information_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1946407753)

(The choice of key concepts here is somewhat arbitrary, and loosely follows the spirit if not the letter of the CRM Core vocabulary element selection.  It is based in part on past experience of terms used to capture descriptions of cultural objects.  The online CRM reference can be used to navigate to related relations and concepts: these terms provide some entry points for exploring the richness of CIDOC CRM.)

NOTE: in reviewing CIDOC CRM for this note, the most specific concept I can see covering musical performance (as opposed to making a recording) appears to be E7 Activity.  This is supported by [FRBRoo](http://www.cidoc-crm.org/frbr_inro.html), which models `frbr:F31_Performance` as a direct subclass of `cidoc:E7_Activity`.

@@Example from musical performance - pick on Carolan performance


## W3C PROV basics

See:
* http://www.w3.org/TR/prov-overview/

The goals of PROV are more modest than CRM, viz to capture details of the mechanisms whereby some artifact or information was derived.  It is built around a very small set of core concepts:

* Entities: anything which can have an associated provenance description,
* Activities: processes that consume and create entities, and
* Agents: people, software and other actors that control or guide the conduct of an activity

![PROV core structure](http://www.w3.org/TR/2013/REC-prov-o-20130430/diagrams/starting-points.svg)

(Diagram from W3C PROV-O document, based on an illustration by Jun Zhao)

There are several additional classes and relations defined by PROV that are mostly refinements of these core concepts, or structural elements used to help oirganize provenance descriptions.

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
        prov:wasAssociatedWith ex:NickPerez, ex:Steve_Benford ;
        prov:used ex:Reclaimed_Mahogany_pece, ex:Flamed_Maple_piece, ex:Spruce_piece ;
        prov:qualifiedAsscoiation [
            a prov:Association ;
            prov:agent   ex:NickPerez ;
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
    ex:Performer
        a prov:Role ;
        rdfs:label "Player in a music performance"
        .
    ex:Guitarist
        a prov:Role ;
        rdfs:label "Guitarist"
        .
    ex:Luthier
        a prov:Role, rdfs:Class ;
        rdfs:label "Guitar maker"
        .

[(Graphical view)](https://rawgit.com/gklyne/DMO_Experiment/master/Coquette_prov_example.svg)

<!--
![Graphical view](https://raw.githubusercontent.com/gklyne/DMO_Experiment/master/Coquette_prov_example.svg)
-->

Notes:

* `ex:Luthier` used here as both `prov:Role` and `rdfs:Class`.  I think this is OK.




## Using CIDOC CRM alongside PROV

### Classes

PROV class      | CIDOC CRM class       | Comment
----------      | ---------------       | -------
`prov:Entity`   | `cidoc:E70_Thing`     | Common sublasses used might be `cidoc:E22_Man-Made_Object` and `cidoc:E73_Information_Object`
`prov:Activity` | `cidoc:E5_Event`      | `cidoc:E7_Activity` is subclass that further constrains to "actions intentionally carried out by instances of E39 Actor", which I would judge the appropriate mapping for a musical performance
`prov:Agent`    | `cidoc:E39_Actor`     | The CIDOC CRM term here is specifically intended to refer to people, individuall or collectively.  I am not seeing an obvious candidate for software agents, which are also covered by the term `prov:Agent`:  `cidoc:E29_Design_or_Procedure` might be applicable for this, but is discouraged by the qualification "in particular [...] deliberate human activities that may result in the modification or production of instances of E24 Physical Thing".  The term `cidoc:E73_Information_Object` could apply, but is not so specific.

Note, CIDOC CRM defines a number of intervening casses in the overall hierarchy:

* [cidoc:E1_Entity](http://erlangen-crm.org/docs/ecrm/current/#anchor-2008326450)
    * [cidoc:E2_Temporal_Entity](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1417799787)
        * [cidoc:E4_Period](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1061023960)
            * [cidoc:E5_Event](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-680408142)
                * [cidoc:E7_Activity](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-121262637)
                    * _with several refinements of activity_
    * [cidoc:E52_Time-Span](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-811589756)
    * [cidoc:E53_Place](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor696186594)
    * [cidoc:E77_Persistent_Item](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1004077320)
        * [cidoc:E70_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1522879172)
            * [cidoc:E71_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1603840578)
                * [cidoc:E28_Conceptual_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-2048876791)
                    * [cidoc:E90_Symbolic_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-395324192), 
                      [cidoc:E89_Propositional_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1515448893)
                        * [cidoc:E73_Information_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1946407753)
                            * [cidoc:E29_Design_or_Procedure](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1838075452)
                            * [cidoc:E31_Document](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1608334786)
                * [cidoc:E24_Physical_Man-Made_Thing](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor1321167518)
                    * [cidoc:E22_Man-Made_Object](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-579380173)
        * [cidoc:E39_Actor](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor-1773481576)
            * [cidoc:E21_Person](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor2037747319)
            * [cidoc:E74_Group](http://erlangen-crm.org/docs/ecrm/current/index.html#anchor766224729)


### Properties

Properties describe relations between designated types of entities.  Complex paths are presented using a notation based on SPARQL property paths (http://www.w3.org/TR/sparql11-query/#propertypaths)

PROV property               | CIDOC CRM encoding                                                | Comment
-------------               | ------------------                                                | -------
**Properties of `prov:Entity`** | | 
`prov:wasDerivedFrom`       | `cidoc:P108i_was_produced_by` / `cidoc:P16_used_specific_object`  | Entity-to-entity derivation
`prov:wasGeneratedBy`       | `cidoc:P108i_was_produced_by`                                     |
`prov:wasAttributedTo`      | `cidoc:P108i_was_produced_by` / `prov:P14_carried_out_by`         | Entity-to-agent attribution
**Properties of `prov:Activity`** | | 
`prov:used`                 | `cidoc:P16_used_specific_object`                                  |
`prov:wasInformedBy`        | `cidoc:P16_used_specific_object` / `cidoc:P108i_was_produced_by`  | Activity-to-activity propagation (cf. `prov:wasDevivedFrom`)
`prov:startedAtTime`        | `cidoc:P4_has_time-span`                                          | The `cidoc:E52_Time-Span` time-span encompases start and end times, and the methods for specificating it are quite open.  In previous implementation work, I've introduced additional properties of `cidoc:E52_Time-Span` taking ISO8601/RFC3999 literal values to define a period specifically, which can be used alongside (say) `cidoc:P78_is_identified_by`
`prov:endedAtTime`          | `cidoc:P4_has_time-span`                                          | (see `prov:startedAtTime`)
`prov:wasAssociatedWith`    | `prov:P14_carried_out_by`                                         |
**Properties of `prov:Agent`** | | 
`prov:ActedOnBehalfOf`      |                                                                   | Agent-to-agent delegation of responsibility.  I've not yet identified an easy way to capture this in CIDOC CRM.  I imagine one could create a delegation event that captures the delegation of responsibility with respect to some other designated activity.  Overall, CIDOC CRM seems to be weak on the representation of agency other than directly by a person of group of people.


## Inferences

@@Add notes about inferences to make information available

@@assume: subclass closure, subproperty closure

@@implementation options: materialize inferred triples (forward chaining), logic programming/query rewriting (backward chaining)


## NOTES

### See also FRBRoo

* [Combined FRBRoo/CRM browser](http://erlangen-crm.org/docs/efrbroo/120131/)
* [OWLDoc for FRBRoo](http://erlangen-crm.org/docs/efrbroo/120131/ontologies/120131___1327187983.html)
* [OWLDoc for CIDOC CRM](http://erlangen-crm.org/docs/efrbroo/120131/ontologies/120111___1747105019.html) imported by OWLDoc FRBRoo.
* [F31 performance](http://erlangen-crm.org/docs/efrbroo/120131/classes/F31Performance___1836504452.html) is subclass of E7 Activity.  (Ack. Terhi Nurmikko-Fuller for spotting this.)


### Erlangen bugs/oddities noted:

Erlangen university have created an OWL version of CIDOC CRM, with online hyperlinked documentation.  See [http://erlangen-crm.org/docs/ecrm/current/]().

* P108 (URI has 'i', name does not)  (The CRM spec used to use different property names, e.g. P108_... and P108I_...)
* E29 (typo after ISO)
* Term hyperlinks appear to be auto-generated rather than based on the CRM term, which leads to a possibility that the hyperlinks may not be stable across different versons of the spec.

### Graphing RDF data

* http://rhizomik.net/html/redefer/rdf2svg-form/


