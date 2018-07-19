# Vehicle Manufacture Network

> This network tracks the manufacture of vehicles from an initial order request through to their completion by the manufacturer. A regulator is able to provide oversight throughout this whole process. 

## Installing pre-requisites
> To run this this tutorial, preferably download Oracle link: https://www.virtualbox.org/wiki/Downloads and install Ubuntu 16.04(Xenial) link: http://releases.ubuntu.com/16.04/

For full step and explanation on how to install localy, kindly check this link: http://releases.ubuntu.com/16.04/

If you start with a newly installed Ubuntu OS do install NPM, Curl, and Node with the following Command
```
sudo apt install curl
sudo apt install npm
sudo apt install nodejs-legacy
```

Here are the summary of important step by step to install Hyperledger Composer & Composer Rest Server on your VM:
> Important, don't use sudo on any of the following command

1. Download the script to install all the necessary prerequisites and make sure you have the necessary priviliged
```
curl -O https://hyperledger.github.io/composer/latest/prereqs-ubuntu.sh

chmod u+x prereqs-ubuntu.sh
```
2. Run the script
```
./prereqs-ubuntu.sh
```
3. Install Composer CLI tools:
```
npm install -g composer-cli
```
4. Instaling Composer Rest Server
```
npm install -g composer-rest-server
```
5. Useful utility for generating application assets (Optional)
``` 
npm install -g generator-hyperledger-composer
```
6. Yeoman is a tool for generating applications, which utilises generator-hyperledger-composer (Optional)
```
npm install -g yo
```
7. Install the Composer Playground
```
npm install -g composer-playground
```
8. Go to your directory of choices (in these example Document) and get the Hyperledger Fabric
```
cd Documents

mkdir ~/fabric-dev-servers && cd ~/fabric-dev-servers

curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
tar -xvf fabric-dev-servers.tar.gz
```
9. Make sure you use Hyperledger version 1.1 for Hyperledger Composer version 17.0 or newer
```
export FABRIC_VERSION=hlfv11
```
10. In the fabric-dev-servers folder run the following script 
> In case docker access was denied, run the folowing command `usermod -a -G docker $USER` and then logout and login again.
```
./downloadFabric.sh
```
11. Start your Hyperldeger Fabric
```
./startFabric.sh
```
12. Create your peerAdmin Card
```
 ./createPeerAdminCard.sh
```
13. Test your newly installed system by opening the Composer Playground
```
composer-playground
```
14. Open up your browser
```
localhost:8080
```

Now you can import the sample business network by navigating through the GUI. 
Clone this repositories to get the prebuilt `vehicle-manufacutre-network@0.2.6.bna` file to be imported.

To generate the REST server, see the [Generate Rest Server Section](#generate-rest-server)

When you are finish exploring your installation, don't forget to turn off your fabric network by going into your fabric-dev-servers folder then run these script
```
./stopFabric.sh
```

Congratulation! You have successfully installed blockchain network on your local machine!


## Rerun the business network

Here are a handy guides on how to turn back on your network. 
Everytime you want to use your hyperledger playground or after restarting your computer/vm follow these step:
1. Go to your fabric-dev-servers folder in the following tutorial it's located on your Documents folder
```
cd Documents/fabric-dev-servers
```
2. Run the startFabric script (keep in mind that you don't need to rerun the createPeerAdminCard.sh)
```
./startFabric.sh
```
3. Go to your Blockchain-Tutorial directory 
```
cd ..
cd Blockchain-Tutorial
```

Before proceeding to the next step, it is assumed that you have created the .bna file after you finished editing your code and ready to deploy the updates
by running this command
```
composer archive create -t dir -n .
```

3. Installing your business network
```
composer network install --card PeerAdmin@hlfv1 --archiveFile vehicle-manufacture-network@0.2.6.bna
```
4. Start your business network
```
composer network start --networkName vehicle-manufacture-network --networkVersion 0.2.6 --networkAdmin admin --networkAdminEnrollSecret adminpw --card PeerAdmin@hlfv1 --file networkadmin.card
```
5. Check whether your business network is running (Optional)
```
composer network ping --card admin@vehicle-manufacture-network
```
6. Run Composer Playground
```
composer-playground
```
7. Open up your browser
```
localhost:8080
```

## Generate Rest Server
> This step will guide you to create and open rest server for testing purposes only, for production enviroment with identity management kindly follow this link: https://hyperledger.github.io/composer/latest/integrating/enabling-rest-authentication.html after that, continue with this article link: https://hyperledger.github.io/composer/latest/integrating/enabling-multiuser.html

To generate a simple rest server capabilty for interacting with the business network, follow this step:
1. Open terminal then run these command to generate the rest server
```
composer-rest-server
```
2. Enter `admin@vehicle-manufacture-network` as the card name.

3. Select never use namespaces when asked whether to use namespaces in the generated API.

4. Select No when asked whether to secure the generated API.

5. Select Yes when asked whether to enable event publication.

6. Select No when asked whether to enable TLS security.

7. Open up your browser
```
localhost:3000
```

## Models within this business network

### Participants
`Person` `Manufacturer` `Regulator`

### Assets

`Order` `Vehicle`

### Transactions

`PlaceOrder` `UpdateOrderStatus` `SetupDemo`

### Events

`PlaceOrderEvent` `UpdateOrderStatusEvent`

## Example use of this business network
A `Person` uses a manufacturer's application to build their desired car and order it. The application submits a `PlaceOrder` transaction which creates a new `Order` asset containing the details of the vehicle the `Person` wishes to have made for them. The `Manufacturer` begins work on the car and as it proceeds through stages of production the `Manufacturer` submits `UpdateOrderStatus` transactions to mark the change in status for the `Order` e.g. updating the status from PLACED to SCHEDULED_FOR_MANUFACTURE. Once the `Manufacturer` has completed production for the `Order` they register the car by submitting an `UpdateOrderStatus` transaction with the status VIN_ASSIGNED (also providing the VIN to register against) and a `Vehicle` asset is formally added to the registry using the details specified in the `Order`. Once the car is registered then the `Manufacturer` submits an `UpdateOrderStatus` transaction with a status of OWNER_ASSIGNED at which point the owner field of the `Vehicle` is set to match the orderer field of the `Order`. The regulator would perform oversight over this whole process. 

## Testing this network within playground
Navigate to the **Test** tab and then submit a `SetupDemo` transaction:

```
{
  "$class": "org.acme.vehicle_network.SetupDemo"
}
```

This will generate three `Manufacturer` participants, fourteen `Person` participants, a single `Regulator` participant and thirteen `Vehicle` assets. 

Next order your car (an orange Arium Gamora) by submitting a PlaceOrder transaction:

```
{
  "$class": "org.acme.vehicle_network.PlaceOrder",
  "orderId": "1234",
  "vehicleDetails": {
    "$class": "org.acme.vehicle_network.VehicleDetails",
    "make": "resource:org.acme.vehicle_network.Manufacturer#Arium",
    "modelType": "Gamora",
    "colour": "Sunburst Orange"
  },
  "options": {
    "trim": "executive",
    "interior": "red rum",
    "extras": ["tinted windows", "extended warranty"]
  },
  "orderer": "resource:org.acme.vehicle_network.Person#Paul"
}
```

This `PlaceOrder` transaction generates a new `Order` asset in the registry and emits a `PlaceOrderEvent` event.

Now simulate the order being accepted by the manufacturer by submitting an `UpdateOrderStatus` transaction:

```
{
  "$class": "org.acme.vehicle_network.UpdateOrderStatus",
  "orderStatus": "SCHEDULED_FOR_MANUFACTURE",
  "order": "resource:org.acme.vehicle_network.Order#1234"
}
```

This `UpdateOrderStatus` transaction updates the orderStatus of the `Order` with orderId 1234 in the asset registry and emits an `UpdateOrderStatusEvent` event.

Simulate the manufacturer registering the vehicle with the regulator by submitting an `UpdateOrderStatus` transaction:

```
{
  "$class": "org.acme.vehicle_network.UpdateOrderStatus",
  "orderStatus": "VIN_ASSIGNED",
  "order": "resource:org.acme.vehicle_network.Order#1234",
  "vin": "abc123"
}
```

This `UpdateOrderStatus` transaction updates the orderStatus of the `Order` with orderId 1234 in the asset registry, create a new `Vehicle` based of that `Order` in the asset registry and emits an `UpdateOrderStatusEvent` event. At this stage as the vehicle does not have an owner assigned to it, its status is declared as OFF_THE_ROAD.

Next assign the owner of the vehicle using an `UpdateOrderStatus` transaction:

```
{
  "$class": "org.acme.vehicle_network.UpdateOrderStatus",
  "orderStatus": "OWNER_ASSIGNED",
  "order": "resource:org.acme.vehicle_network.Order#1234",
  "vin": 'abc123'
}
```

This `UpdateOrderStatus` transaction updates the orderStatus of the `Order` with orderId 1234 in the asset registry, update the `Vehicle` asset with VIN abc123 to have an owner of Paul (who we intially marked as the orderer in the `PlaceOrder` transaction) and status of ACTIVE and also emits an `UpdateOrderStatusEvent` event.

Finally complete the ordering process by marking the order as `DELIVERED` through submitting another `UpdateOrderStatus` transaction:

```
{
  "$class": "org.acme.vehicle_network.UpdateOrderStatus",
  "orderStatus": "DELIVERED",
  "order": "resource:org.acme.vehicle_network.Order#1234"
}
```

This `UpdateOrderStatus` transaction updates the orderStatus of the `Order` with orderId 1234 in the asset registry and emits an `UpdateOrderStatusEvent` event.

This Business Network definition has been used to create demo applications that simulate the scenario outlined above. You can find more detail on these at https://github.com/hyperledger/composer-sample-applications/tree/master/packages/vehicle-manufacture

## Permissions in this business network for modelled participants
Within this network permissions are outlines for the participants outlining what they can and can't do. The rules in the permissions.acl file explicitly ALLOW participants to perform actions. Actions not written for a participant in that file are blocked.
### Regulator
`RegulatorAdminUser` - Gives the regulator permission to perform ALL actions on ALL resources

### Manufacturer
`ManufacturerUpdateOrder` - Allows a manufacturer to UPDATE an Order asset's data only using an UpdateOrderStatus transaction. The manufacturer must also be specified as the *vehicleDetails.make* in the Order asset.

`ManufacturerUpdateOrderStatus` - Allows a manufacturer to CREATE and READ UpdateOrderStatus transactions that refer to an order that they are specified as the *vehicleDetails.make* in. 

`ManufacturerReadOrder` - Allows a manufacturer to READ an Order asset that they are specified as the *vehicleDetails.make* in.

`ManufacturerCreateVehicle` - Allows a manufacturer to CREATE a vehicle asset only using a UpdateOrderStatus transaction. The transaction must have an *orderStatus* of VIN_ASSIGNED and the Order asset have the manufacturer specified as the *vehicleDetails.make*.

`ManufacturerReadVehicle` - Allows a manufacturer to READ a Vehicle asset that they are specified as the *vehicleDetails.make* in.

### Person
`PersonMakeOrder` - Gives the person permission to CREATE an Order asset only using a PlaceOrder transaction. The person must also be specified as the *orderer* in the Order asset.

`PersonPlaceOrder` - Gives the person permission to CREATE and READ PlaceOrder transactions that refer to an order that they are specified as *orderer* in.

`PersonReadOrder` - Gives the person permission to READ an Order asset that they are specified as the *orderer* in.

## License <a name="license"></a>
Hyperledger Project source code files are made available under the Apache License, Version 2.0 (Apache-2.0), located in the LICENSE file. Hyperledger Project documentation files are made available under the Creative Commons Attribution 4.0 International License (CC-BY-4.0), available at http://creativecommons.org/licenses/by/4.0/.