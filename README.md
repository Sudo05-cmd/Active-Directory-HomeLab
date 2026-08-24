# Enterprise Active Directory & Core Network Infrastructure Home Lab

## Project Overview
This home lab demonstrates the deployment, configuration, and management of an enterprise-grade corporate network infrastructure. Utilizing a hypervisor environment, I built a functional Active Directory domain controller from scratch, joined a Windows client endpoint, automated network configurations, and implemented centralized security compliance and file-sharing structures.

## Technologies & Skills Used
*   **Hypervisor:** VMware Workstation Pro
*   **Operating Systems:** Windows Server 2022 Evaluation Edition (Domain Controller & Client Endpoint)
*   **Directory Services:** Active Directory Domain Services (AD DS)
*   **Core Networking:** DNS, DHCP (Role Deployment & Scope Configuration)
*   **Systems Management:** Group Policy Objects (GPOs), Organization Units (OUs), Administrative Templates
*   **Security & Access Control:** Role-Based Access Control (RBAC), NTFS Permissions, Share Permissions, Least Privilege Principle

## Architecture & Configuration Details
*   **Domain Controller VM:** 
    *   **IP Address:** 192.168.10.10 (Static)
    *   **Roles:** AD DS, DNS, DHCP
*   **Client VM:**
    *   **IP Address:** Dynamically assigned via DHCP Scope (`192.168.10.100 - 192.168.10.200`)
    *   **Preferred DNS:** 192.168.10.10 (Pointing to Domain Controller)

---

## Lab Implementation Steps

### Phase 1: Active Directory Domain Services (AD DS) & DNS Setup
1. Installed Windows Server 2022 and configured a static IP address (`192.168.10.10`).
2. Deployed the AD DS and DNS roles via Server Manager.
3. Promoted the server to a Domain Controller, creating a new forest root domain.
4. Created a custom Organizational Unit (OU) named `Lab Users` to separate standard corporate accounts from built-in administrative folders.
5. Provisioned a standard domain user account (`john.smith`) to simulate an everyday corporate endpoint user.

### Phase 2: Client Domain Join & Network Alignment
1. Configured the Client VM network adapter within VMware Workstation to align on the same private virtual network segment as the server.
2. Manually adjusted the Client's Preferred DNS properties to target the Domain Controller IP.
3. Utilized `sysdm.cpl` to securely authenticate with Domain Admin credentials and join the client machine to the Active Directory domain.

### Phase 3: DHCP Role Deployment & Hypervisor Disambiguation
1. Deployed the **DHCP Server Role** on the Domain Controller to eliminate the need for manual endpoint IP management.
2. Configured an active IP scope (`192.168.10.100 - .200`) and set Scope Options to automatically broadcast the DC's IP as the gateway and primary DNS server.
3. Resolved an IP distribution conflict by auditing the hypervisor's settings and disabling VMware's built-in virtual DHCP service, allowing the Windows Server to become the authoritative DHCP hand-off device on the subnet.
4. Verified success on the client endpoint via `ipconfig /all`, confirming automatic acquisition of a scoped IP and correct DNS pointers.

### Phase 4: Hardening Security via Group Policy (GPOs)
1. Created and linked a new GPO named `Block Control Panel` directly to the `Lab Users` OU.
2. Configured User Administrative Templates to prohibit standard users from accessing the Windows Settings application and Control Panel utilities.
3. Verified enforcement on the client endpoint by running `gpupdate /force` and testing user restrictions, ensuring a standard user cannot modify localized device states.

### Phase 5: Enterprise File Sharing & Drive Mapping Preferences
1. Provisioned a `CompanyShare` folder on the server's root storage.
2. Configured dual-layer permissions to align with the Principle of Least Privilege:
    *   **Share Permissions:** Open read/write access to authenticated users.
    *   **NTFS (Security) Permissions:** Explicit permissions restricted strictly to authorized domain groups and accounts, preventing unauthorized directory transversal.
3. Created a `Map Corporate Drive` GPO utilizing Group Policy Preferences (*Drive Maps*) to automatically map the shared UNC path to the `S:\` drive letter on login.
4. Successfully verified mapping and browse capabilities directly from the standard client's File Explorer profile.
Initial Documentation Commit
