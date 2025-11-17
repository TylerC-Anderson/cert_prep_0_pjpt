
## Quick-Use

**Commands**:
- To run bloodhound:
    - `sudo neo4j console`
        - navigate to http://localhost:7474/ (or whichever portnum given by the CLI output after putting in the above command)
    - back in CLI `sudo bloodhound`
        - login to webconsole with neo4j:neo4j (or the appropriate password if you've logged in before)
    - to setup an ingester: `sudo bloodhound-python -d ADNAME -u uname -p Password -ns TARGIPADDR -c all`
        - back in bloodhound webconsole, `Upload Data` -> import the data you collected with the ingester

- To run Plumhound, you NEED bloodhound run from above:
    - to init: `sudo python3 PlumHound.py --easy -p BloodhoundPassword`
    - then: `suod python3 PlumHound.py -x tasks/default.tasks -p BloodhoundPassword`

*Tools*:
- 

## General

**Objectives**:
While within the Bloodhound webconsole, you can:
- discover paths to high value targets
- Mark users/machines as owned
- find Roastable services
- See all flags for users and policies
- See map of Domain and Trees
- much more

**Overview**:

## Glossary