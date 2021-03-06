============
determinator
============


.. image:: https://img.shields.io/badge/License-MIT-yellow.svg
        :target: https://opensource.org/licenses/MIT
        :alt: License: MIT

.. image:: https://img.shields.io/badge/code%20style-black-000000.svg
        :target: https://github.com/psf/black
        :alt: Code style: black

**DISCLAIMER - BETA PHASE**

*This package is currently in a beta phase.*

Package for terminology management with the TermBase eXchange (TBX) format

* Free software: MIT license

to determinate [ **determ**-i-nate ]
------------------------------------

    *v.intr*, **determinated**, **determinating**

    1. To extract terms from one of more text documents and output results in the TermBase eXchange (TBX) format.

Features
--------

- Extract expert terminology from documents in the NLP Annotation Format (NAF)

- Generate and read TermBase eXchange (TBX) files according to ISO 30042:2019 (TBX-DNB dialect)

- Add references and term notes from other sources (for example European IATE term bases)


Overview of the idea
--------------------

We generate an empty TBX document with

::

    t = determinator.TbxDocument()
    t.generate(params = {"sourceDesc": "TBX file, created via dnb/determinator"})

Then we extract terms from the Solvency II Delegated Acts (Dutch version) in NAF:

::

    # create terms dictionary of subset of languages
    terms = {}
    for language in ['NL', 'EN', 'DE', 'FR', 'ES', 'ET', 'DA', 'SV']:
        DOC_FILE = "..\\..\\nafigator-data\\data\\legislation\\Solvency II Delegated Acts - "+language+".naf.xml"
        doc = nafigator.NafDocument().open(DOC_FILE)
        determinator.merge_terms_dict(terms, nafigator.extract_terms(doc))

Then we create a termbase

::

    # Create an empty TermBase
    t = determinator.TbxDocument()
    t.generate(params = {"sourceDesc": "TBX file, created via dnb/determinator"})
    t.create_tbx_from_terms_dict(terms=terms, params = {'concept_id_prefix': 'tbx_'})

Then we add references from the InterActive Terminology for Europe (IATE) dataset:

::

    # read the IATE file
    IATE_FILE = "..//data//iate//IATE_export.tbx"
    ref = determinator.TbxDocument().open(IATE_FILE)
    t.copy_from_tbx(reference=ref)

Then we add termnotes from the Dutch Lassy dataset (the small one) including basic insurance terms:

::

    # read the lassy file
    LASSY_FILE = "..//data//lassy//lassy_with_insurance.tbx"
    lassy = determinator.TbxDocument().open(LASSY_FILE)
    t.add_termnotes_from_tbx(reference=lassy, params={'number_of_word_components':  5})

Then we have a termbase with:

::

    <conceptEntry id="249">
     <descrip type="subjectField">insurance</descrip>
     <xref>IATE_2246604</xref>
     <ref>https://iate.europa.eu/entry/result/2246604/en</ref>
     <langSec xml:lang="nl">
      <termSec>
       <term>solvabiliteitskapitaalvereiste</term>
       <termNote type="partOfSpeech">noun</termNote>
       <note>source: data/Solvency II Delegated Acts - NL.txt (#hits=331)</note>
       <termNote type="termType">fullForm</termNote>
       <descrip type="reliabilityCode">9</descrip>
       <termNote type="lemma">solvabiliteits_kapitaalvereiste</termNote>
       <termNote type="grammaticalNumber">singular</termNote>
       <termNoteGrp>
        <termNote type="component">solvabiliteits-</termNote>
        <termNote type="component">kapitaal-</termNote>
        <termNote type="component">vereiste</termNote>
       </termNoteGrp>
      </termSec>
     </langSec>
     <langSec xml:lang="en">
      <termSec>
       <term>SCR</term>
       <termNote type="termType">abbreviation</termNote>
       <descrip type="reliabilityCode">9</descrip>
      </termSec>
      <termSec>
       <term>solvency capital requirement</term>
       <termNote type="termType">fullForm</termNote>
       <descrip type="reliabilityCode">9</descrip>
       <termNote type="partOfSpeech">noun, noun, noun</termNote>
       <note>source: data/Solvency II Delegated Acts - EN.txt (#hits=266)</note>
      </termSec>
     </langSec>
     <langSec xml:lang="fr">
      <termSec>
       <term>capital de solvabilit?? requis</term>
       <termNote type="termType">fullForm</termNote>
       <descrip type="reliabilityCode">9</descrip>
       <termNote type="partOfSpeech">noun, adp, noun, adj</termNote>
       <note>source: ../nafigator-data/data/legislation/Solvency II Delegated Acts - FR.txt (#hits=198)</note>
      </termSec>
      <termSec>
       <term>CSR</term>
       <termNote type="termType">abbreviation</termNote>
       <descrip type="reliabilityCode">9</descrip>
      </termSec>
     </langSec>
    </conceptEntry>

* a reference is included to concept '2246604' from the IATE dataset. From that reference, we can for example derive that the official European term for this concept in English is 'solvency capital requirement' and in German 'Solvenzkapitalanforderung' and that the term is defined in Directive 2009/138/EC (Solvency II).

* termNotes include the partOfSpeech, lemma and morpohoFeats derived from the Lassy dataset (in Dutch). This dataset was extended with insurance related word components and terms that were not included in the Lassy dataset.

* also included are the word components of a term. The Dutch language, like the German language, often glues components together to construct new words instead of using separate words like the English language.

Datasets
--------

* `Interactive Terminology for Europe <https://iate.europa.eu/home/>`_

* `Lassy klein corpus <https://taalmaterialen.ivdnt.org/download/lassy-klein-corpus6/>`_


The TermBase eXchange format
----------------------------

* `Introduction to TermBase eXchange (TBX) Version 3 <https://www.tbxinfo.net/>`_

* `Converting TBX to RDF <https://www.w3.org/community/bpmlod/wiki/Converting_TBX_to_RDF/>`_

* `The Lexicon Model for Ontologies <https://lemon-model.net/>`_
