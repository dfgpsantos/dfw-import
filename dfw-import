#!/bin/bash

NSXUSER='youruserhere'
#NSXPASS='yourpasswordhere'
NSXMAN='yournsxmanagerhere'

read -s -p "Password: " NSXPASS

#txt files cleanup

rm -rf *.txt


#FW Rule list processing

RULES="rules.list"
RULESEQ=0

for RULELINE in `cat $RULES`

do

RULESEQ=$(( $RULESEQ + 1))

SECTIONNAME=`echo $RULELINE | awk '{print $1}' FS=\;`
RULENAME=`echo $RULELINE | awk '{print $2}' FS=\;`
SOURCEVAR=`echo $RULELINE | awk '{print $3}' FS=\;`
DESTINATIONVAR=`echo $RULELINE | awk '{print $4}' FS=\;`
PROTOVAR=`echo $RULELINE | awk '{print $5}' FS=\;`
PORTVAR=`echo $RULELINE | awk '{print $6}' FS=\;`
ACTIONVAR=`echo $RULELINE | awk '{print $7}' FS=\;`
LOGVAR=`echo $RULELINE | awk '{print $8}' FS=\;`

#Adjust the Variables


if [[ "$LOGVAR" == "log" ]];
then

LOGVAR="true"

else

LOGVAR="false"

fi

if [[ "$PROTOVAR" == "ICMP" ]];

then

SERVICEVAR="/infra/services/ICMP-ALL"


fi

if [[ "$PROTOVAR" == "ANY" ]] ||  [[ "$PROTOVAR" == "TCP" ]] ||  [[ "$PROTOVAR" == "ANY" ]] ;

then

SERVICEVAR="ANY"

fi


if [[ "$PROTOVAR" == "TCP" ]] ||  [[ "$PROTOVAR" == "UDP" ]];

then

SIMPLEVAR="NO"

else

SIMPLEVAR="YES"

fi


#Create the DFW Rule

cat > DFW-$SECTIONNAME$RULENAME.txt << EOL
{
    "rules" : [
      {
        "description": "DFW Rule for $RULENAME",
        "display_name": "$RULENAME",
        "source_groups": [
          $SOURCEVAR
        ],
        "destination_groups": [
          $DESTINATIONVAR
        ],
        "services": [ "$SERVICEVAR" ],
        "service_entries": [ {
          "l4_protocol": "$PROTOVAR",
          "source_ports": [ ],
          "destination_ports": [ $PORTVAR ],
          "resource_type": "L4PortSetServiceEntry"
          } ],
        "logged": "$LOGVAR",
        "action": "$ACTIONVAR"
      }

    ]
}

EOL

#Remove unecessary lines for ICMP and ANY


if [[ "$SIMPLEVAR" == "YES" ]];
then

sed -i -e '13,18d' DFW-$SECTIONNAME$RULENAME.txt

fi

#Adjust the spaces for json

sed -i 's/\"\,\"/\"\,\ \"/g' DFW-$SECTIONNAME$RULENAME.txt


#Apply the DFW Configuration using curl

echo "Creating DFW Rule for $RULENAME with the Source IP Address $SOURCEVAR and Destination IP Address $DESTINATIONVAR for $PROTOVAR and $PORTVAR"

curl -k --user $NSXUSER:$NSXPASS https://$NSXMAN/policy/api/v1/infra/domains/default/security-policies/$SECTIONNAME -X PATCH --data @DFW-$SECTIONNAME$RULENAME.txt -H "Content-Type: application/json"

sleep 1


done


#Clean up

read -p "Do you want to delete the .txt files created for migration? (Y/N)"  -r CHOICE
echo
if [[ $CHOICE =~ ^[Yy]$ ]];

then

echo "Deleting .txt files"
rm -rf *.txt

else
echo "Saving the .txt files"

fi

echo "Task completed! $RULESEQ rule(s) have been configured in NSX $NSXMAN."
