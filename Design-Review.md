#  SONiC-OTN Design Review comments
The following document contains design review comments for SONiC-OTN. The general design is to use the existing SONiC Architecture and Framework as much as possible so that we leverage on it and make changes on top of it. It helps in not breaking the existing architecture and add modular SONiC_OTN modules on top of it to co-exist. After going through the existing documentation the below design addresses them.

## New Design diagram

![image](https://github.com/vharish02/SONiC-OTN-HLD/blob/main/SONiC-G31-HLD.PNG)

In this design I am introducing 2 new contianers as given below which are exclusively for SONiC-OTN. By adhering to this design we are not changing anything drastic in the exiting SONiC containers and at most might be very little changes if required to support SONiC-OTN.

- Olss container and
- optsyncd.

SONiC architecure depends on redis for communication between the containers and application it becomes easier to integrate with the new optical containers. The Olss container will subscribe to all the optical realted configuration changes and other data that is required to be programed in the ASIC. The 2 processes inside will take care of processing the configuration either coming through CLI or NBI's and optorchagent will push the data to a new database optasic-db or oasic-db. optsyncd contianer will subscribe to those events and process the data and use OTAI APi's to send to Linecard.

Using this design SONiC-OTN containers are independent entity interacting with existing SONiC containers and subscribing to configuration from redis-database. Will be able to get latest code changes of SONiC sub-modules (swss container) integrated automatically and maintaining will be easy, no breakage of existing functionality as OTN code is separate.

Including the dhcp-relay container as there might be a use-case for it in future.

## Merging SONiC-OTN parameters with SAI and syncd containers
SAI is another component that is maintained by OCP which is a completely different org than the SONiC community maintained by Linux Foundation.
As per the earlier design suggestion of integrating the OTN components in SAI., it is good to keep SAI and OTAI separately. We might have to go through OCP community approval for this and will take time. Would be better to have it separate and out of SAI. Similarly the syncd container is used for networking Switch ASIC's, making lot of changes to integrate SONiC-OTN would require lot of testing of existing switching and networking functionality. Would be good to have separate optical syncd container (optsyncd) doing optical related asic programing and have minior changes to integrate with syncd.

## Networking containers coexist with Optical containers
Using this design the SONiC networking containers still exists and run as is along with it we will have 2 new optical container for consuming and processing optical information. Advantage is if we have a node which supports both then this design will work seemlessly. Incase we don't need some containers I believe running it should not pose any harm or would be able to skip running.

## Conclusion
By having separate containers for processing of messages coming from application layer for optical information and updating redis database and processing the line card related asic programing using optical syncd container it is modular easy to maintain and less complexity involved and easily manageable with the existing SONiC architecture with minimal changes.
