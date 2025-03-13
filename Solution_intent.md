I would like to align over the expectations of documentation and way of working for Ringfencing PoC:

•	A functional (end user) description of the ringfencing network building block as described in the ringfencing pattern. 
o	A solution design would be nice but is not strictly necessary.  
•	A user manual on how to make use of the ringfencing network building block. The user manual should tell us how to: 
o	Request a new ringfence and which options/choices we have. 
o	Move a system into the ringfence
o	Deploy a new system into the ringfence
o	Define the access to and from the system(s)
The goal of the description is to allow a team to determine if the ringfence is a suitable solution to mitigate certain types of risks, and what the benefits and limitations are. 
The goal of the user manual is to allow a team to migrate to a ringfenced network independency and efficiently. It should clearly describe the steps, required information and timelines. 

[26-02] Aligned, action points defined, documentation to be delivered
[13-03] Re-align to discuss the documentation provided (rescheduled due to illness)


---

## RINGFENCE DC
### DC ESG IMPLEMENTATION

- **Date:** 02/03/2025
- **Author:** William Kimandu
- **Location:** Campus DC
- **Version:** 0.1

### Document History
| Revision Date | Revised By       | Summary of Changes | Version |
|---------------|------------------|--------------------|---------|
| 02/03/2025    | Infra ART DC     | Initial draft      | 0.1     |

### Review
| Name            | Department | Date of Issue | Version |
|-----------------|------------|---------------|---------|
| William Kimandu | Campus DC  | 02/03/2025    | 0.1     |

### Contacts
| Name / Team      | Department / Role | Email |
|------------------|-------------------|-------|
| IT OPS           | TCS               |       |
| IT Campus DC ART | ASML/NTT/TCS      |       |

---

## Contents
1. Prerequisites
2. Initial Navigation
3. ESG Creation
   - Advanced settings
   - PCTag: Auto-assign
4. ESG Selector Configuration
5. Contract Configuration
   - Apply to ESG
6. Verification Steps
   - Check Endpoint Association
7. Common ESG Configurations
8. Troubleshooting
9. Best Practices

---

## Detailed ESG Configuration Guide in Cisco ACI APIC

### Prerequisites
- APIC admin access
- Target tenant permissions
- Planned ESG structure
- Identified endpoints and contracts

### Initial Navigation
1. Launch APIC GUI (https://{APIC-IP})
2. Login with admin credentials
3. Navigate: `Tenants > {your-tenant} > Application Profiles`

### ESG Creation
1. Right-click "Endpoint Security Groups"
2. Select "Create Endpoint Security Group"
3. Fill in basic information:
   - Name: esg-prod-vmware
   - Description: Production VMware Environment
   - VRF: {select-appropriate-vrf}

#### Advanced settings
- Intra ESG isolation: Disabled
- Preferred Group Member: No
- PCTag: Auto-assign

### ESG Selector Configuration
1. In ESG > Selectors tab:
   - Name: vmware-prod-selector
   - Match Type: EPG
   - EPG: epg-3378
   - Additional Criteria:
     - MAC: 00:50:56:*
     - Subnet: 172.31.44.224/28

### Contract Configuration
1. Create Contract:
   - Name: prod-to-mgmt
   - Scope: tenant
   - Subject: mgmt-access
   - Filters:
     - https (TCP/443)
     - ssh (TCP/22)
     - snmp (UDP/161)
2. Apply to ESG:
   - Provided Contracts tab:
     - Contract: prod-to-mgmt
     - Type: provided
   - Consumed Contracts tab:
     - Contract: dmz-access
     - Type: consumed

### Verification Steps
1. Operational View:
   - Navigate: Operations > EP Tracker
   - Filter by:
     - ESG Name
     - MAC prefix
     - IP subnet
2. Check Endpoint Association:
   - Navigate: Tenant > Application Profiles > ESGs
   - Select ESG > Operational tab
   - Verify:
     - Endpoint count
     - Contract status
     - Policy deployment

### Common ESG Configurations
- **DMZ ESG**
- **Management ESG**
  - Name: esg-mgmt
  - Selectors:
    - Match EPG: epg-1751
    - Type: physical-only
  - Contracts:
    - Provided:
      - mgmt-access

### Troubleshooting
- **Endpoint not appearing in ESG:**
  - Verify selector criteria
  - Check endpoint attributes
  - Confirm EPG association
- **Contract issues:**
  - Verify contract scope
  - Check filter entries
  - Confirm provider/consumer relationship
- **Common commands:**
  ```bash
  # Check ESG configuration
  moquery -c fvESg
  
  # Verify endpoint association
  moquery -c fvCEp -f 'fvCEp.esg=="esg-prod-vmware"'
  
  # Check contract deployment
  moquery -c vzBrCP -f 'vzBrCP.name=="prod-to-mgmt"'
  ```

### Best Practices
- **Naming Conventions:**
  - Use consistent prefixes (esg-, contract-, filter-)
  - Include environment indicator (prod-, dev-, test-)
  - Add purpose suffix (-web, -db, -app)
- **Documentation:**
  - Document all ESG configurations
  - Map contract relationships
  - Keep endpoint inventory updated
- **Security:**
  - Follow least-privilege principle
  - Regular contract audit
  - Monitor ESG membership changes

---

Guidance - Design DOC

The ESG solution design must answer:
Are there means to provide segmentation within constraints of scale, current operating model and effectiveness with available tooling:

Isolation

Segmentation in the Datacenter

Macro / < Micro segments

Virtualization vs. BareMetal

Re-IP or not - IP addressing

Phase 2.
Visibility of flows - IDS / IPS

Define terms:
Provide segmentation based on current SBB 
Given an asset list - of systems which have full network access among them (not qualified) and have undetermined network access filtering and inspection capabilities, determine how we can reduce the threat level by using networking constructs to segment the network further.
Ideal: 
Current State:
When the customer has an application dependency mapping, and a clear communication needs between / among the applications and flow patterns, for external communication, shared services, utility services, Mgmt. & Control Plane and Tier-0 services. In ASML there exists a Solution Building Block that can be leveraged
Future state:
Provide segmentation based on new ESG construct and Service Graphs
When the foregoing is missing, and the customer has a clear threat and vulnerability profile matrix, the DC Connectivity services can leverage (SDN constructs) and evolve the Solution Building Block to meet evolving security needs, with granular segmentation and traffic redirection to security functions

<Wait>
SBB = solution building Block

What is the status on the 1st option: SBB compliant segmentation? We need to deliver a going in for the customer, so they can asses which assets belongs to what segment. After that we can discuss a clear plan to segment with “open” rules and how they need to analyze east-west traffic.

Service design and request

Selectors 
IP address 
Mac-address
Tags 
Need VMM integration so that vcenter can push the tags

---

I would like to align over the expectations of documentation and way of working for Ringfencing PoC:
 
• A functional (end user) description of the ringfencing network building block as described in the ringfencing pattern. 
o A solution design would be nice but is not strictly necessary.  
• A user manual on how to make use of the ringfencing network building block. The user manual should tell us how to: 
o Request a new ringfence and which options/choices we have. 
o Move a system into the ringfence
o Deploy a new system into the ringfence
o Define the access to and from the system(s)
The goal of the description is to allow a team to determine if the ringfence is a suitable solution to mitigate certain types of risks, and what the benefits and limitations are. 
The goal of the user manual is to allow a team to migrate to a ringfenced network independency and efficiently. It should clearly describe the steps, required information and timelines. 
 
[26-02] Aligned, action points defined, documentation to be delivered
[13-03] Re-align to discuss the documentation provided (rescheduled due to illness)
---

## RINGFENCE DC
### DC ESG IMPLEMENTATION

- **Date:** 02/03/2025
- **Author:** William Kimandu
- **Location:** Campus DC
- **Version:** 0.1

### Document History
| Revision Date | Revised By       | Summary of Changes | Version |
|---------------|------------------|--------------------|---------|
| 02/03/2025    | Infra ART DC     | Initial draft      | 0.1     |

### Review
| Name            | Department | Date of Issue | Version |
|-----------------|------------|---------------|---------|
| William Kimandu | Campus DC  | 02/03/2025    | 0.1     |

### Contacts
| Name / Team      | Department / Role | Email |
|------------------|-------------------|-------|
| IT OPS           | TCS               |       |
| IT Campus DC ART | ASML/NTT/TCS      |       |

---

## Contents
1. Prerequisites
2. Initial Navigation
3. ESG Creation
   - Advanced settings
   - PCTag: Auto-assign
4. ESG Selector Configuration
5. Contract Configuration
   - Apply to ESG
6. Verification Steps
   - Check Endpoint Association
7. Common ESG Configurations
8. Troubleshooting
9. Best Practices

---

## Detailed ESG Configuration Guide in Cisco ACI APIC

### Prerequisites
- APIC admin access
- Target tenant permissions
- Planned ESG structure
- Identified endpoints and contracts

### Initial Navigation
1. Launch APIC GUI (https://{APIC-IP})
2. Login with admin credentials
3. Navigate: `Tenants > {your-tenant} > Application Profiles`

### ESG Creation
1. Right-click "Endpoint Security Groups"
2. Select "Create Endpoint Security Group"
3. Fill in basic information:
   - Name: esg-prod-vmware
   - Description: Production VMware Environment
   - VRF: {select-appropriate-vrf}

#### Advanced settings
- Intra ESG isolation: Disabled
- Preferred Group Member: No
- PCTag: Auto-assign

### ESG Selector Configuration
1. In ESG > Selectors tab:
   - Name: vmware-prod-selector
   - Match Type: EPG
   - EPG: epg-3378
   - Additional Criteria:
     - MAC: 00:50:56:*
     - Subnet: 172.31.44.224/28

### Contract Configuration
1. Create Contract:
   - Name: prod-to-mgmt
   - Scope: tenant
   - Subject: mgmt-access
   - Filters:
     - https (TCP/443)
     - ssh (TCP/22)
     - snmp (UDP/161)
2. Apply to ESG:
   - Provided Contracts tab:
     - Contract: prod-to-mgmt
     - Type: provided
   - Consumed Contracts tab:
     - Contract: dmz-access
     - Type: consumed

### Verification Steps
1. Operational View:
   - Navigate: Operations > EP Tracker
   - Filter by:
     - ESG Name
     - MAC prefix
     - IP subnet
2. Check Endpoint Association:
   - Navigate: Tenant > Application Profiles > ESGs
   - Select ESG > Operational tab
   - Verify:
     - Endpoint count
     - Contract status
     - Policy deployment

### Common ESG Configurations
- **DMZ ESG**
- **Management ESG**
  - Name: esg-mgmt
  - Selectors:
    - Match EPG: epg-1751
    - Type: physical-only
  - Contracts:
    - Provided:
      - mgmt-access

### Troubleshooting
- **Endpoint not appearing in ESG:**
  - Verify selector criteria
  - Check endpoint attributes
  - Confirm EPG association
- **Contract issues:**
  - Verify contract scope
  - Check filter entries
  - Confirm provider/consumer relationship
- **Common commands:**
  ```bash
  # Check ESG configuration
  moquery -c fvESg
  
  # Verify endpoint association
  moquery -c fvCEp -f 'fvCEp.esg=="esg-prod-vmware"'
  
  # Check contract deployment
  moquery -c vzBrCP -f 'vzBrCP.name=="prod-to-mgmt"'   
