| Title                | Hacktool Ruler                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | This events that are generated when using the hacktool Ruler by Sensepost                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0007: Discovery](https://attack.mitre.org/tactics/TA0007)</li><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1087: Account Discovery](https://attack.mitre.org/techniques/T1087)</li><li>[T1075: Pass the Hash](https://attack.mitre.org/techniques/T1075)</li><li>[T1114: Email Collection](https://attack.mitre.org/techniques/T1114)</li><li>[T1059: Command-Line Interface](https://attack.mitre.org/techniques/T1059)</li></ul>  |
| Data Needed          | <ul><li>[DN_0079_4776_computer_attempted_to_validate_the_credentials_for_an_account](../Data_Needed/DN_0079_4776_computer_attempted_to_validate_the_credentials_for_an_account.md)</li><li>[DN_0057_4625_account_failed_to_logon](../Data_Needed/DN_0057_4625_account_failed_to_logon.md)</li><li>[DN_0004_4624_windows_account_logon](../Data_Needed/DN_0004_4624_windows_account_logon.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1087: Account Discovery](../Triggers/T1087.md)</li><li>[T1075: Pass the Hash](../Triggers/T1075.md)</li><li>[T1114: Email Collection](../Triggers/T1114.md)</li><li>[T1059: Command-Line Interface](../Triggers/T1059.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>Go utilities that use staaldraad awesome NTLM library</li></ul>  |
| Development Status   |  Development Status wasn't defined for this Detection Rule yet  |
| References           | <ul><li>[https://github.com/sensepost/ruler](https://github.com/sensepost/ruler)</li><li>[https://github.com/sensepost/ruler/issues/47](https://github.com/sensepost/ruler/issues/47)</li><li>[https://github.com/staaldraad/go-ntlm/blob/master/ntlm/ntlmv1.go#L427](https://github.com/staaldraad/go-ntlm/blob/master/ntlm/ntlmv1.go#L427)</li><li>[https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4776](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4776)</li><li>[https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624)</li></ul>  |
| Author               | Florian Roth |


## Detection Rules

### Sigma rule

```
title: Hacktool Ruler
id: 24549159-ac1b-479c-8175-d42aea947cae
description: This events that are generated when using the hacktool Ruler by Sensepost
author: Florian Roth
date: 2017/05/31
modified: 2019/07/26
references:
    - https://github.com/sensepost/ruler
    - https://github.com/sensepost/ruler/issues/47
    - https://github.com/staaldraad/go-ntlm/blob/master/ntlm/ntlmv1.go#L427
    - https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4776
    - https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624
tags:
    - attack.discovery
    - attack.execution
    - attack.t1087
    - attack.t1075
    - attack.t1114
    - attack.t1059
logsource:
    product: windows
    service: security
detection:
    selection1:
        EventID: 
          - 4776
        Workstation: 'RULER'
    selection2:
        EventID:
          - 4624
          - 4625
        WorkstationName: 'RULER'
    condition: (1 of selection*)
falsepositives:
    - Go utilities that use staaldraad awesome NTLM library
level: high

```





### splunk
    
```
(((EventID="4776") Workstation="RULER") OR ((EventID="4624" OR EventID="4625") WorkstationName="RULER"))
```






### Saved Search for Splunk

```
b'# Generated with Sigma2SplunkAlert\n[Hacktool Ruler]\naction.email = 1\naction.email.subject.alert = Splunk Alert: $name$\naction.email.to = test@test.de\naction.email.message.alert = Splunk Alert $name$ triggered \\\nList of interesting fields:   \\\ntitle: Hacktool Ruler status:  \\\ndescription: This events that are generated when using the hacktool Ruler by Sensepost \\\nreferences: [\'https://github.com/sensepost/ruler\', \'https://github.com/sensepost/ruler/issues/47\', \'https://github.com/staaldraad/go-ntlm/blob/master/ntlm/ntlmv1.go#L427\', \'https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4776\', \'https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624\'] \\\ntags: [\'attack.discovery\', \'attack.execution\', \'attack.t1087\', \'attack.t1075\', \'attack.t1114\', \'attack.t1059\'] \\\nauthor: Florian Roth \\\ndate:  \\\nfalsepositives: [\'Go utilities that use staaldraad awesome NTLM library\'] \\\nlevel: high\naction.email.useNSSubject = 1\nalert.severity = 1\nalert.suppress = 0\nalert.track = 1\nalert.expires = 24h\ncounttype = number of events\ncron_schedule = */10 * * * *\nallow_skew = 50%\nschedule_window = auto\ndescription = This events that are generated when using the hacktool Ruler by Sensepost\ndispatch.earliest_time = -10m\ndispatch.latest_time = now\nenableSched = 1\nquantity = 0\nrelation = greater than\nrequest.ui_dispatch_app = sigma_hunting_app\nrequest.ui_dispatch_view = search\nsearch = (((EventID="4776") Workstation="RULER") OR ((EventID="4624" OR EventID="4625") WorkstationName="RULER")) | stats values(*) AS * by _time | search NOT [| inputlookup Hacktool_Ruler_whitelist.csv] | collect index=threat-hunting marker="sigma_tag=attack.discovery,sigma_tag=attack.execution,sigma_tag=attack.t1087,sigma_tag=attack.t1075,sigma_tag=attack.t1114,sigma_tag=attack.t1059,level=high"\n\n\n'
```