# Ansys2022R1Deployment
Deployment script for Ansys 2022 R1 when SCCM deployment fails

This script requires several things to correct Ansys' error

1. Admin credentials on the machines you are installing to
2. The installation files for Ansys placed on a network share
3. A .bat file to install the software placed in the same network share folder as the setup.exe file (example file provided)
4. A CSV file containing the list of hostnames you wish to deploy Ansys to. Please note, CSV must use 'Name' as the column header for the hostnames
