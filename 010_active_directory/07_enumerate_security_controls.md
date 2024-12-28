# Enumerating Security Controls

**Windows Defender :**

Get-MpComputerStatus

**AppLocker :**

Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

**PowerShell Constrained Language Mode :**

$ExecutionContext.SessionState.LanguageMode
ConstrainedLanguage

**LAPS :**

Find-LAPSDelegatedGroups

**Using Find-AdmPwdExtendedRights :**

Find-AdmPwdExtendedRights

**Using Get-LAPSComputers :**

Get-LAPSComputers
