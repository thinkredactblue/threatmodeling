<h1> Practical Threat Modeling with MITRE ATT&CK </h1>

# Day 1 / Lab 1: Threat Modeling - Am I a Target?

Follow along using the MITRE ATT&CK Navigator https://mitre-attack.github.io/attack-navigator/enterprise/#

# Day 1 / Lab 2: Slicing and Dicing ATT&CK with MITRE Assistant Insights xe

Mitre Assistant allows you to slice and dice the ATT&CK matrix quickly to get meaningful insights of what that matrix has to offer.

Mitre Assistant was created and is maintained by Carlos Diaz (@dfirence)

Follow along using https://github.com/dfirence/ma-insights-xe

More info on the project on Carlos' GitHub repo: https://github.com/dfirence/ma-insights-xe

- Overview statistics - https://ma-insights.vercel.app/overview
- Stats by adversary - https://ma-insights.vercel.app/adversaries
- Stats by tactics, techniques, and data source - https://ma-insights.vercel.app/enterprise
- Stats by weapons (malware and tools) - https://ma-insights.vercel.app/weapons

# Day 2 / Lab 1 - Cyballistics – Follow the Weapons with VirusTotal 

Follow the instructions given in class to obtain your temporary Virustotal Intelligence license. Login to VT with your account, and using the advanced search modifiers in VT Enterprise, find relevant patterns or behaviors for:

- Microsoft Word documents (doc) that have macros, that execute files and that have Cyrillic language in the metadata.

- PHP files named gate.php that have been observed as part of network communications (useful to find samples that exhibit specific Command and Control behavior).

- Samples where wscript is observed executing .vbs files, through behavioral analysis (sandboxes)

- Google drive URLs with more than 20 detections.

- PCAPs tagged with exploit-kit.

- Samples that modify a specific registry key for persistence purposes.

- PDFs that contain JavScript and contains an automatic action (perhaps to launch the previous JavaScript).

We already identified that Hacme Cats needs to focus on increasing visibility and detection on Spearphishing Attachments, among others techniques. 
  
Spearphishing Attachments is a Sub-Technique of Technique T1566 in the MITRE ATT&CK Matrix, part of the Initial Access Tactic. 

Since we're studying Phishing, and more specifically Spear-Phishing techniques, we can ask the 'Google of malware' to research samples exhibiting malicious behaviors. A quick look at MITRE's description for this technique show us threat groups that are quite active at the time of this writing, and that have been involved with popular campaigns like Ryuk ransomware, Emotet, Trickbot or Bazar. We're talking about the Wizard Spider or UNC1878 group: https://attack.mitre.org/groups/G0102/

Notice how this group makes active use of Microsoft documents containing macros or PDFs containing malicious links to download either Emotet, Bokbot, TrickBot, or Bazar. 

Let's make this more actionable and try to expand our understanding of how attackers (any attacker, not just Wizard Spider) make use of these documents. In order to try to extract a study base of malicious PDFs from VirusTotal the first idea that comes to our minds is to use a search query as simple as:

```text
type:pdf positives:5+
```

This query returns PDF files that are detected by five antivirus solutions or more. Make sure you are signed in with your activated account, since this is a privileged functionality in VT.

We can learn more of these samples by selecting any of them. The tags added by VT gives us an indication of the behavior of these files. For example, we can observe that some of them make use of sandbox evasion techniques to defeat analysts and security products. Notice the `detect-debug-environment` tag. 

Compare the results of this search, with the results of the next one. This query searches for Excel spreadsheets (xls) that execute other processes and that have been reported by more than 10 antivirus engines:

```text
type:xls have:execution_parents p:10+
```

Notice how the tags give us more context on what these samples are doing. You can pivot and expand the results by clicking on any of those tags.

Let's do a similar search but this time for RTF documents that execute other processes and that have been reported by 10 AV engines or more:

```text
type:rtf have:execution_parents p:10+
```

Notice how again the tags give us more context on what these samples are doing. Not only we know they contain macros, as expected, but we can also see the vulnerability that's being exploited, in this case CVE-2017-11882. You can pivot and expand the results by clicking on any of those tags.

As you can see, despite being patched for years already, attackers are still relying on this old remote code execution vulnerability in Microsoft Office to infect victims with malware. You can find more information on CVE-2017-11882 and associated mitigations here: https://socprime.com/blog/cve-2017-11882-two-decades-old-vulnerability-in-microsoft-office-still-actively-leveraged-for-malware-delivery/

Another well known vulnerability (CVE-2017-0199) allows remote code execution against old versions of Microsoft Office and Windows, and has been a very popular vector of attack, with more than 5,600 malware samples exploiting the issue this year, including 15 malicious samples reported from Egypt, according to BlackBerry: https://www.darkreading.com/cyberattacks-data-breaches/india-linked-sidewinder-group-pivots-to-hacking-maritime-targets

We can now pivot and expand our search, to find any samples submitted to VT that have been tagged with this specific Common Vulnerability and Exposure (CVE) identifier and with the tag exploit:

```text
tag:cve-2017-11882 tag:exploit
```

VT Intelligence allows you to run very specific searches. Now take some time to 'fly solo' and experiment with additional searches like:

- Microsoft Word documents (doc) that have macros, that execute files and that have Cyrillic language in the metadata.

```text
type:doc p:10+ tag:macros tag:run-file metadata:Cyrillic
```

- PHP files named gate.php that have been observed as part of network communications (useful to find samples that exhibit specific Command and Control behavior).

```text  
behavior_network:"gate.php"
```

- Samples where wscript is observed executing .vbs files, through behavioral analysis (sandboxes)

```text  
behavior_files:"wscript.exe" behavior_files:".vbs"
```

- Google drive URLs with more than 20 detections:

```text
p:20+ itw:"drive.google.com" 
```  

- PCAPs tagged with exploit-kit:

```text
type:pcap tag:"exploit-kit" 
```  

- Samples that modify a specific registry key for persistence purposes:

```text
behavior_registry:"Software\Microsoft\Windows\CurrentVersion\Run"
```  

- PDFs that contain JavScript and contains an automatic action (perhaps to launch the previous JavaScript):

```text
type:pdf tag:autoaction tag:js-embedded
```  

For a full reference on VT file search modifiers check this reference:

https://support.virustotal.com/hc/en-us/articles/360001385897-File-search-modifiers

# Day 2 / Lab 2 - Threat Modeling – Visibility & Coverage

DeTT&CT aims to assist blue teams in using ATT&CK to score and compare data log source quality, visibility coverage, detection coverage and threat actor behaviours.

The DeTT&CT framework consists of a Python tool, YAML administration files, the DeTT&CT Editor and scoring tables for the different aspects.

## Setup

1. Install docker for your platform
    - https://docs.docker.com/get-docker/

2. Get the latest image for DeTT&CT from Docker Hub
    `docker pull rabobankcdc/dettect:latest`

   ** Note, if you have Apple Silicon M1/M2 you'll need to run this command to specify the platform `export DOCKER_DEFAULT_PLATFORM=linux/x86_64/v8`

4. Execute the appropriate command to create the container and mount the input and output directories. These directories will be created in your current working directory.

- Linux and MacOS: 
    `docker run -p 8080:8080 -v $(pwd)/output:/opt/DeTTECT/output -v $(pwd)/input:/opt/DeTTECT/input --name dettect -it --rm rabobankcdc/dettect:latest /bin/bash`

- Windows (cmd.exe): 
    `docker run -p 8080:8080 -v %cd%/output:/opt/DeTTECT/output -v %cd%/input:/opt/DeTTECT/input --name dettect -it --rm rabobankcdc/dettect:latest /bin/bash`
    
- PowerShell: 
    `docker run -p 8080:8080 -v ${PWD}/output:/opt/DeTTECT/output -v ${PWD}/input:/opt/DeTTECT/input --name dettect -it --rm rabobankcdc/dettect:latest /bin/bash`

- To start the container when it is no longer running (this should bring you straight back into the container with an interactive Bash shell)

    `docker start -i dettect`

## Using DeTT&CT Editor

1. Using the local editor 

    `python dettect.py editor &`

2. Alternatively you can also use the online editor

    https://rabobank-cdc.github.io/dettect-editor/#/home

Don't forget to save the YAML files once edited.

## Data sources

Data sources are the raw logs or events generated by systems, security appliances, network devices, etc. ATT&CK has over 30 different data sources, which are further divided into over 90 data components. All of the data components are included in this framework.

For each data source, among others, the data quality can be scored. The scoring tables are available here: https://github.com/rabobank-cdc/DeTTECT/raw/master/scoring_table.xlsx

Within ATT&CK these data sources are listed within the techniques themselves. To see what data sources are available per platform, see https://github.com/rabobank-cdc/DeTTECT/wiki/Data-sources-per-platform

If you want to use the online editor, the sample datasource file is available here:
https://github.com/rabobank-cdc/DeTTECT/blob/master/sample-data/data-sources-endpoints.yaml

1. Generate your first JSON layer to import in ATT&CK Navigator based on the sample data sources file provided by DeTT&CT. This file represents the data sources available in your environment. This gives you a rough overview of your visibility coverage. Often, this is the first step in getting an overview of your actual visibility coverage.
       
    `python dettect.py ds -fd sample-data/data-sources-endpoints.yaml -l`

    The results of executing this command will be saved to the `output` directory both on your machine and inside the container. 

2. Import the JSON file into ATT&CK Navigator to see what techniques you would have visibility for based on those data sources 

    For layers files created by DeTT&CT, it is best to use this URL to the Navigator as it will make sure metadata in the layer file does not have a yellow underline: 
    
    https://mitre-attack.github.io/attack-navigator/#comment_underline=false

3. Try to create a new YAML file with only traditional network based data sources and see what techniques visibility would be available in your environment. Save it to your system and load it again on Navigator. 

4. You can build on this last layer to visualize the gaps between your visibility and the techniques used by threat groups of interest. Use the **selection controls** panel's **multi-select** control to select all of the techniques used by the FIN7 threat groups; then close the **multi-select** drop-down.

## Adjust Visibility & Detection

It is possible and recommended to adjust the visibility score per technique based on expert knowledge and the previously defined quality of your data sources. This will be required in cases when a single data source that we have available is not be sufficient for at least a minimum detection level for a specific technique. And hence, the visibility score based on the number of data sources needs to be adjusted.

To administrate your detection and visibility scores per ATT&CK technique, you can use this YAML file https://github.com/rabobank-cdc/DeTTECT/wiki/YAML-administration-techniques_v1_2

For details on how to adjust visibility and detection quality for each technique see https://github.com/rabobank-cdc/DeTTECT/wiki/How-to-use-the-framework#detection

We do not recommend doing this for every technique due to the administrative burden involved, but rather on a case by case basis.

5. Adjust visibility and detection assigning individual scores to each technique. The following command will generate a file of techniques that will only include those for which we have defined data sources previously. 

    `python dettect.py ds -fd sample-data/data-sources-endpoints.yaml --yaml`

6. Open up the file with the browser, using the local or the online editor:

    `python dettect.py editor`

7. Adjust the individual scores if needed and save the file. 

8. Generate a new JSON file for ATT&CK Navigator with your adjusted visibility coverage:

    `python dettect.py v -ft sample-data/techniques-administration-endpoints.yaml -l`

9. Do the same with detection coverage. This command will generate a new JSON file with your adjusted detection coverage:

    `python dettect.py d -ft sample-data/techniques-administration-endpoints.yaml -l`

10. Import the resulting JSON files into Navigator to see what techniques you would have visibility and detection for based on your adjusted input. 
    
    https://mitre-attack.github.io/attack-navigator/#comment_underline=false

## Threat Actor heatmaps

You can generate an ATT&CK Navigator layer file based on threat actor group data in ATT&CK. Or your threat actor data stored in a group YAML file.

11. The below-generated layer file contains a heat map based on all threat actor data within ATT&CK. The darker the color in the heat map, the more often the technique is being used among groups. Please note that, like all data, there is bias.

    `python dettect.py g`

12. Generate a JSON file with the heatmap corresponding to the groups we're interested in (FIN6, FIN7, FIN8) and import it into Navigator.

    `python dettect.py g -g 'fin7,fin8,fin6'`

## Visibility & Detection Gap Analysis (Purple Teaming)

You can compare a group YAML file with, for example, data on a red team exercise or a specific threat actor group with your detection or visibility. DeTT&CT can generate an ATT&CK Navigator layer file in which the differences are visually shown with a legend explaining the colors.

13. Generate a custom heatmap in a JSON file using the selected threat actor report from Red Canary and the sample YAML template for detection: 

    `python dettect.py g -g threat-actor-data/20210331-RedCanary.yaml -o sample-data/techniques-administration-endpoints.yaml -t detection`

14. Do the same for visibility

    `python dettect.py g -g threat-actor-data/20210331-RedCanary.yaml -o sample-data/techniques-administration-endpoints.yaml -t visibility`

# Day 2 / Lab 3 - From ATT&CK to D3FEND 

1. Use the Jupyter Notebook here

    https://github.com/aboutsecurity/jupyter-notebooks

2. Click on Launch BINDER 

    https://mybinder.org/v2/gh/aboutsecurity/jupyter-notebooks/HEAD

3. Open the JSON parser folder and click on the json-parser.ipynb (Jupyter Notebook)

4. Go to ATT&CK extractor and copy all the techniques obtained as a result of running the script

    https://d3fend.mitre.org/tools/attack-extractor/?q=%5B%5D

5. Examine the mapping results. Do you miss any defensive countermeasures?



