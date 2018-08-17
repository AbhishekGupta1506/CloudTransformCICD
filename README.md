# CloudTransformCICD
CloudTransformCICD

In order to migrate on-prmeise 912 setup to 10.3 using cce following the following steps:
1. Make sure firewall is off
2. Bootstrap the cce 
3. Import the template under below location "C:\Users\Administrator\sag\cc\profiles\CCE\data\templates\composite\sag-ic-migration"
4. Run below command:
sagcc exec templates composite apply sag-ic-migration -i C:\Users\Administrator\sag\cc\profiles\CCE\data\templates\composite\sag-ic-migration\env.properties