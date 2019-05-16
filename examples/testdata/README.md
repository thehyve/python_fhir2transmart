
## Prepare tools

```bash
# Setup Python virtualenv
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install fhir2transmart claml2transmart

# Download transmart-copy
curl -f -L https://repo.thehyve.nl/service/local/repositories/releases/content/org/transmartproject/transmart-copy/17.1-HYVE-5.9-RC3/transmart-copy-17.1-HYVE-5.9-RC3.jar -o transmart-copy.jar
```

## Data loading

### HL7 FHIR resource ontology

```bash
# Create an output directory
mkdir -p output/fhir
# Apply the mapping
claml2transmart http://hl7.org/fhir/R4 fhir.xml output/fhir
# Upload the data
PGUSER=tm_cz PGPASSWORD=tm_cz java -jar transmart-copy.jar -d output/fhir
```

### ICD-10-GM ontology

Example: the ICD-10-GM (German modification of ICD-10) is available at [icd10gm2019syst-claml.zip].

[icd10gm2019syst-claml.zip]: https://www.dimdi.de/dynamic/.downloads/klassifikationen/icd-10-gm/version2019/icd10gm2019syst-claml.zip

```bash
# Unzip and navigate to the classification directory
mkdir icd10gm2019syst-claml
cd icd10gm2019sys-claml
unzip ../icd10gm2019syst-claml.zip
# Create an output directory
mkdir -p output/icd10gm2019
# Apply the mapping
claml2transmart http://dimdi.de/icd10gm2019 Klassifikationsdateien/icd10gm2019syst_claml_20180921.xml output/icd10gm2019
# Upload the data
PGUSER=tm_cz PGPASSWORD=tm_cz java -jar transmart-copy.jar -d output/icd10gm2019
```

### Resources

```bash
# Create an output directory
mkdir -p output/observations
# Apply the mapping
fhir2transmart fhir/ output/observations
# Upload the data
PGUSER=tm_cz PGPASSWORD=tm_cz java -jar transmart-copy.jar -d output/observations
```
