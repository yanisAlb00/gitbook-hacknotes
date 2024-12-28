# Notetaking & Organization

## Notetaking Sample Structure :

Attack Path - An outline of the entire path if you gain a foothold during an external penetration test or compromise one or more hosts (or the AD domain) during an internal penetration test. Outline the path as closely as possible using screenshots and command output will make it easier to paste into the report later and only need to worry about formatting.

Credentials - A centralized place to keep your compromised credentials and secrets as you go along.

Findings - We recommend creating a subfolder for each finding and then writing our narrative and saving it in the folder along with any evidence (screenshots, command output). It is also worth keeping a section in your notetaking tool for recording findings information to help organize them for the report.

Vulnerability Scan Research - A section to take notes on things you've researched and tried with your vulnerability scans (so you don't end up redoing work you already did).

Service Enumeration Research - A section to take notes on which services you've investigated, failed exploitation attempts, promising vulnerabilities/misconfigurations, etc.

Web Application Research - A section to note down interesting web applications found through various methods, such as subdomain brute-forcing. It's always good to perform thorough subdomain enumeration externally, scan for common web ports on internal assessments, and run a tool such as Aquatone or EyeWitness to screenshot all applications. As you review the screenshot report, note down applications of interest, common/default credential pairs you tried, etc.

AD Enumeration Research - A section for showing, step-by-step, what Active Directory enumeration you've already performed. Note down any areas of interest you need to run down later in the assessment.

OSINT - A section to keep track of interesting information you've collected via OSINT, if applicable to the engagement.

Administrative Information - Some people may find it helpful to have a centralized location to store contact information for other project stakeholders like Project Managers (PMs) or client Points of Contact (POCs), unique objectives/flags defined in the Rules of Engagement (RoE), and other items that you find yourself often referencing throughout the project. It can also be used as a running to-do list. As ideas pop up for testing that you need to perform or want to try but don't have time for, be diligent about writing them down here so you can come back to them later.

Scoping Information - Here, we can store information about in-scope IP addresses/CIDR ranges, web application URLs, and any credentials for web applications, VPN, or AD provided by the client. It could also include anything else pertinent to the scope of the assessment so we don't have to keep re-opening scope information and ensure that we don't stray from the scope of the assessment.

Activity Log - High-level tracking of everything you did during the assessment for possible event correlation.

Payload Log - Similar to the activity log, tracking the payloads you're using (and a file hash for anything uploaded and the upload location) in a client environment is critical. More on this later.

## Tmux

```
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
touch .tmux.conf

cat .tmux.conf 

List of plugins

set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-logging'

Initialize TMUX plugin manager (keep at bottom)
run '~/.tmux/plugins/tpm/tpm'

tmux source ~/.tmux.conf 
```

```
set -g history-limit 50000
```

```
tmux new -s sessionname
```

To recreate the above example first start a new tmux session: tmux new -s sessionname. Once in the session type [Ctrl] + [B] + [Shift] + [%] (prefix + [Shift] + [%]) to split the panes vertically (replace the [%] with ["] to do a horizontal split). We can then move from pane to pane by typing [Ctrl] + [B] + [O] (prefix + [O]).

Finally, we can clear the pane history by typing [Ctrl] + [B] followed by [Alt] + [C] (prefix + [Alt] + [C]).


## Structure of storage

```
mkdir -p ACME-IPT/{Admin,Deliverables,Evidence/{Findings,Scans/{Vuln,Service,Web,'AD Enumeration'},Notes,OSINT,Wireless,'Logging output','Misc Files'},Retest}

tree ACME-IPT/

ACME-IPT/
├── Admin
├── Deliverables
├── Evidence
│   ├── Findings
│   ├── Logging output
│   ├── Misc Files
│   ├── Notes
│   ├── OSINT
│   ├── Scans
│   │   ├── AD Enumeration
│   │   ├── Service
│   │   ├── Vuln
│   │   └── Web
│   └── Wireless
└── Retest
```

