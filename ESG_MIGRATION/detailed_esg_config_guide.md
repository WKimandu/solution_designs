 Detailed ESG Configuration Guide in Cisco ACI APIC

 Prerequisites
- APIC admin access
- Target tenant permissions
- Planned ESG structure
- Identified endpoints and contracts

 1. Initial Navigation
![APIC Login](placeholder_for_login_screenshot.png)
1. Launch APIC GUI (https://{APIC-IP})
2. Login with admin credentials
3. Navigate: `Tenants > {your-tenant} > Application Profiles`

 2. ESG Creation
![ESG Creation](placeholder_for_esg_creation.png)
1. Right-click "Endpoint Security Groups"
2. Select "Create Endpoint Security Group"
3. Fill in basic information:   ```
   Name: esg-prod-vmware
   Description: Production VMware Environment
   VRF: {select-appropriate-vrf}   ```
4. Advanced settings:
   - Intra ESG isolation: Disabled
   - Preferred Group Member: No
   - PCTag: Auto-assign

 3. ESG Selector Configuration
![Selector Config](placeholder_for_selector.png)
1. In ESG > Selectors tab:   ```yaml
   Name: vmware-prod-selector
   Match Type: EPG
   EPG: epg-3378
   Additional Criteria:
     - MAC: 00:50:56:*
     - Subnet: 172.31.44.224/28   ```

 4. Contract Configuration
![Contract Setup](placeholder_for_contract.png)
1. Create Contract:   ```yaml
   Name: prod-to-mgmt
   Scope: tenant
   Subject: mgmt-access
   Filters:
     - https (TCP/443)
     - ssh (TCP/22)
     - snmp (UDP/161)   ```

2. Apply to ESG:
   - Provided Contracts tab:     ```yaml
     Contract: prod-to-mgmt
     Type: provided     ```
   - Consumed Contracts tab:     ```yaml
     Contract: dmz-access
     Type: consumed     ```

 5. Verification Steps
![Verification](placeholder_for_verify.png)
1. Operational View:   ```
   Navigate: Operations > EP Tracker
   Filter by:
     - ESG Name
     - MAC prefix
     - IP subnet   ```

2. Check Endpoint Association:   ```
   Navigate: Tenant > Application Profiles > ESGs
   Select ESG > Operational tab
   Verify:
     - Endpoint count
     - Contract status
     - Policy deployment   ```

 6. Common ESG Configurations

 DMZ ESG 

 Management ESG
```yaml
Name: esg-mgmt
Selectors:
  - Match EPG: epg-1751
    Type: physical-only
Contracts:
  Provided:
    - mgmt-access
```

 7. Troubleshooting
1. Endpoint not appearing in ESG:
   - Verify selector criteria
   - Check endpoint attributes
   - Confirm EPG association

2. Contract issues:
   - Verify contract scope
   - Check filter entries
   - Confirm provider/consumer relationship

3. Common commands:
   ```bash
   # Check ESG configuration
   moquery -c fvESg
   
   # Verify endpoint association
   moquery -c fvCEp -f 'fvCEp.esg=="esg-prod-vmware"'
   
   # Check contract deployment
   moquery -c vzBrCP -f 'vzBrCP.name=="prod-to-mgmt"'
   ```

 8. Best Practices
1. Naming Conventions:
   - Use consistent prefixes (esg-, contract-, filter-)
   - Include environment indicator (prod-, dev-, test-)
   - Add purpose suffix (-web, -db, -app)

2. Documentation:
   - Document all ESG configurations
   - Map contract relationships
   - Keep endpoint inventory updated

3. Security:
   - Follow least-privilege principle
   - Regular contract audit
   - Monitor ESG membership changes