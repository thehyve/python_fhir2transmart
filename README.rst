################################################################################
Example FHIR to TranSMART loader
################################################################################

|Build status| |codecov| |pypi| |downloads|

.. |Build status| image:: https://travis-ci.org/thehyve/python_fhir2transmart.svg?branch=master
   :alt: Build status
   :target: https://travis-ci.org/thehyve/python_fhir2transmart/branches
.. |codecov| image:: https://codecov.io/gh/thehyve/python_fhir2transmart/branch/master/graph/badge.svg
   :alt: codecov
   :target: https://codecov.io/gh/thehyve/python_fhir2transmart
.. |pypi| image:: https://img.shields.io/pypi/v/fhir2transmart.svg
   :alt: PyPI
   :target: https://pypi.org/project/fhir2transmart/
.. |downloads| image:: https://img.shields.io/pypi/dm/fhir2transmart.svg
   :alt: PyPI - Downloads
   :target: https://pypi.org/project/fhir2transmart/

This package contains a mapper that reads data from `HL7 FHIR`_ (STU 3 or R4) resources
and translates them to the data model of the TranSMART_ platform,
an open source data sharing and analytics platform for translational biomedical research.

It also provides a utility that applies the mapper and writes the translated data to tab-separated files
that can be loaded into a TranSMART database using the transmart-copy_ tool.

The FHIR reader is based on the fhirclient_ package, writing to TranSMART is based on transmart-loader_.

⚠️ Note: this is a very preliminary version, still under development.
Issues can be reported at https://github.com/thehyve/python_fhir2transmart/issues.

.. _`HL7 FHIR`: https://hl7.org/fhir
.. _TranSMART: https://github.com/thehyve/transmart_core
.. _transmart-copy: https://github.com/thehyve/transmart-core/tree/dev/transmart-copy
.. _fhirclient: https://pypi.org/project/fhirclient
.. _transmart-loader: https://pypi.org/project/transmart-loader


Installation
------------

The package requires Python 3.6.

To install ``fhir2transmart``, do:

.. code-block:: bash

  pip install fhir2transmart

Or from source:

.. code-block:: bash

  git clone https://github.com/thehyve/python_fhir2transmart.git
  cd python_fhir2transmart
  pip install .


Run tests (including coverage) with:

.. code-block:: console

  python setup.py test


Usage
-----

Read input from a JSON file ``input.json`` and write the output in transmart-copy
format to ``/path/to/output``. The output directory should be
empty of not existing (then it will be created).

.. code-block:: bash

  # Translate one json file
  fhir2transmart input.json /path/to/output
  # Translate all json files in a directory
  fhir2transmart input_dir /path/to/output

Example data is available at `MITRE SyntheticMass`_. Instructions:

.. _`MITRE SyntheticMass`: https://syntheticmass.mitre.org/download.html

.. code-block:: bash

  # Download 1K Sample Synthetic Patient Records, FHIR STU3 : 20MB
  wget https://syntheticmass.mitre.org/downloads/2017_11_06/synthea_sample_data_fhir_stu3_nov2017.zip
  # unzip creates a directory 'fhir' containing 282MB of json files
  unzip synthea_sample_data_fhir_stu3_nov2017.zip
  # create an output directory
  mkdir output
  # apply the mapping
  fhir2transmart fhir output

This generates the directories ``i2b2metadata`` and ``i2b2demodata`` in the ``output`` directory.
The generated data can be loaded using transmart-copy_:

.. code-block:: console

  # Download transmart-copy:
  curl -f -L https://repo.thehyve.nl/service/local/repositories/releases/content/org/transmartproject/transmart-copy/17.1-HYVE-5.9-RC3/transmart-copy-17.1-HYVE-5.9-RC3.jar -o transmart-copy.jar
  # Load data
  PGUSER=tm_cz PGPASSWORD=tm_cz java -jar transmart-copy.jar -d output


Mapping
-------

The following mapping table shows how FHIR resources are mapped to the
TranSMART data model.

============= =================  ============== ============== ============ =========
FIHR                             TranSMART
-------------------------------  ----------------------------------------------------
Resource type attribute          Class          attribute      concept      modifier
============= =================  ============== ============== ============ =========
Patient_      identifier         PatientMapping identifier
Patient_      gender             Patient        sex
Patient_      gender             Observation    value          Gender
Patient_      birthDate          Observation    value          BirthDate
Patient_      deceased           Observation    value          Deceased
Patient_      deceasedDate       Observation    value          DeceasedDate
------------- -----------------  -------------- -------------- ------------ ---------
Condition_    subject            Observation    patient
Condition_    code               Observation    conceptCode
Condition_    onsetDateTime      Observation    startDate
Condition_    abatementDateTime  Observation    endDate
Condition_    recordedDate       Observation
Condition_    category
------------- -----------------  -------------- -------------- ------------ ---------
Encounter_    identifier         Visit
Encounter_    period.start       Visit          startDate
Encounter_    period.end         Visit          endDate
Encounter_    status             Visit          activeStatusCd
Encounter_    class              Visit          inoutCd
Encounter_    hospitalization    Visit          locationCd
============= =================  ============== ============== ============ =========

.. _Patient: https://www.hl7.org/fhir/patient.html
.. _Condition: https://www.hl7.org/fhir/condition.html
.. _Encounter: https://www.hl7.org/fhir/encounter.html


License
-------

Copyright (c) 2019 The Hyve B.V.

The FHIR to TranSMART loader is licensed under the MIT License. See the file `<LICENSE>`_.
