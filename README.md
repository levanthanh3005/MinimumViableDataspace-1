# Minimum Viable Dataspace

The Minimum Viable Dataspace (MVD) is a sample implementation of a dataspace that leverages the [Eclipse Dataspace Connector (EDC)](https://github.com/eclipse-dataspaceconnector/dataspaceconnector). The main purpose is to demonstrate the capabilities of the EDC, make dataspace concepts tangible based on a specific implementation, and to serve as a starting point to implement a custom dataspace.

The MVD allows developers and decision makers to gauge the current progress of the EDC and its capabilities to satisfy the functionality of a fully operational dataspace.

As a fully decentralized dataspace is hard to imagine, the MVD also serves the purpose of demonstrating how decentralization can be practically implemented.

## MVD Documentation

Developer documentation can be found under [docs/developer](docs/developer/), where the main concepts and decisions are captured as [decision records](docs/developer/decision-records/).

## Gaia-X Hackathon #4

### Day 1

Goal of the hacking session during day 1 are:

- deploy one (and only one for the whole hacking group!) instance of the Registration Service, which exposes a set of REST Api for registering, unregistering and listing the dataspace participants.
  Note that for the sake of simplicity, this deployment will be operated on a dedicated Azure subscription that was specifically preparated before the Hackathon.
- along this Registration services will be deployed 3 fictive participants that will be automatically registered to the datasapce and pre-populated with fictive assets.
  These fictive participants will just be used to illustrate the concepts of the federated catalog during day 1 result showcase session.
- each hacker gets ready to deploy their own connector during the main hacking session of day 2, which consists in:
    - creating their fork of the current repository,
    - [set up their environment](docs/developer/continuous_deployment.md) so that they can leverage on GitHub Actions for deploying the resources.

### Day 2

#### Set up

Once each hacker has its environment setup, objective is that each hacker deploys its own connector and connects to the Registration Service deployed during day 1:

- Connect to GitHub and go to the MVD fork you created during day 1.
- Select the tab called `Actions`.
- Select the workflow called `DeployParticipant`.
- Click on `Run workflow` against the `main` branch to trigger the deployment. You will be prompted to provide some information, such as:
    - the participant name (you can typically put your company name here),
    - a prefix that guarantees the resources name's uniqueness and avoids resource name conflicts. Please, use at most 3 characters, composed of lower case letters and numbers.
    - please keep record of that prefix! As it will be asked when for deprovisioning,
    - your country code, e.g. FR, DE... Note that this information is evaluated by the policy engine of the EDC.
    - the host of the Registration Service deployed during day 1 (will be provided in the chat of the hacking session)

After a couple of minutes, your deployment should be completed. You can check out the resources in the Azure subscription initialized during day 1.
You can check if your connector has been properly registered to the Registration Service by listing the known participants:

```[bash]
curl http://$REGISTRATION_SERVICE_HOST/api/registry/participants
```

In your resource group, you should be able to see a storage account containing `did`. Have a look at what it contains.
You should find a self-description document called `sdd.json`. If you look closely at this document you will find that
it contains the country code you prompted when calling `DeployParticipant`. You can now use the [Gaia-X Compliance Api](https://compliance.gaia-x.eu/docs/#/Participant/ParticipantController_signContent)
to verify that this document is valid. Note that for now, the process for getting this document properly signed by the Compliance Api is by-passed.

If time allows during the second day, the interested hacker can have a look at the #TODO left in the [DeployParticipant workflow](.github/workflows/deployParticipant.yaml) in order to implement the procedure described [here](https://gitlab.com/gaia-x/lab/compliance/gx-compliance).

#### Creation of Assets, Policies and Contract Offers

Next step is then to create some Contract Offer(s) that you want to expose to the other participants. Each offer
is composed of an Asset and a Policy. MVD comes with facilities to create these entities as each participant deployment embeds
a UI which is deployed along with the connector within the same resource group. You can access this UI through your favorite web browser.

#### Contract negotiation and data transfer

TODO

#### Deprovisioning

For any reason, if you want to deprovision your connector, please follow these steps:

- Go to your MVD fork in GitHub.
- Select the tab called `Actions`
- Select the workflow called `DestroyParticipant`
- Please enter the same information as you did when you ran `DeployParticipant`
- This will automatically deprovision all Azure resources, delete the resource group and unregister the participant to the dataspace.
