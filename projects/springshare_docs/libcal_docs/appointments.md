## LibCal Appointments

The LibCal Appointment scheduler is PUL's platform for patrons to make appointments with librarians and library staff members. Appointments are grouped into Location and then Group. We have two locations: Princeton University Library and Research Data Service, for PRDS staff members. Within Princeton University Library, we have several groups to denote clusters of librarians/specialists, services, or specific libraries who offer appointments or consultations. Our current Springshare license includes 125 appointment schedulers; we often bump up against this limit and can request more (for a fee) but also need to deactivate the appointment scheduler when staff leave or are no longer using this feature in order to preserve schedulers.

### Appointment Locations

Appointment locations in LibCal can be set to one of three options: 
- Physical: Meetings are held at a physical location
- Online Managed: Meetings are held only online using our online meeting platform (Zoom)
- Online Hybrid: Meetings are held online by default and either managed by LibCal or managed externally depending on each user's individual configuration. 

Our Princeton University Library location is set to ```Online Hybrid``` because this gives staff the option to set meetings online via Zoom (the default for this location) or to meet in-person, giving the user instructions to a library/office location. Note that this option is not truly "hybrid," as it defaults to providing patrons a link for an online meeting. We have created a custom appointment form template that users can implement if they would like users to have the choice between online and in-person meetings, which includes sending meeting instructions via email for whichever method a user chooses. See the [documentation on Confluence](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/1769498/Setting+Up+Appointments#SettingUpAppointments-CreatingIn-PersonandOnlineMeetingOptionsinAppointments) for how this is configured. 

To edit our Appointment Locations or Groups within them: 

1. In the ```Admin``` drop-down, choose ```Appointments.```
2. Here you will see two locations: Princeton University Library and Research Data Services, each of which has subgroups within them. To edit a Location, choose the gear icon on the right. To add a Location, choose the blue ```+Add Location``` button at the top. 
3. To edit a Group within a location, choose the gear icon on the right of the group's name. You will have options to edit the group, add users, move to another location, and re-order users. 
4. To add a new Group, scroll to the bottom of the existing Groups and click on ```+Add Group.``` 

### Activate/Deactivate Appointment Scheduler

1. In the ```Admin``` drop-down, choose ```Accounts.```
2. To edit a user's appointment scheduler, click on the pencil/paper icon in the ```Action``` column to the right of their name. 
3. In the ```General``` tab, go to ```Appointments Scheduler``` and choose either ```Active``` or ```Inactive``` from the drop-down, depending on whether they are using this feature or not. 

### Add Appointment Availability

By default, users can add their own availability to their calendars and their custom bookig form using the [instructions on Confluence](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/1769498/Setting+Up+Appointments#SettingUpAppointments-SettingupyourLibCalAppointmentAccount). Sometimes, admins will need to view a user's appointment calendar to help troubleshoot an issue or assign appointments to the correct group within a location. To do this: 

1. In the ```Admin``` drop-down, choose ```Appointments.```
2. Within the Princeton University Library or Research Data Service Locations, there are several subgroups. To view a user's account, click on the person icon in the right-hand column. (Note: you can also get here by going to ```Admin``` and ```Accounts``` from the LibCal homepage and clicking the person icon in the Appointments column of their account settings).
3. In the ```My Appointments``` tab, you should see a blue banner that says "You are editing Joe Smith's account" to warn you that you are in an account other than your own. 
4. Click on the ```Availability``` tab at the top, and here you can add/delete availability. Make sure to choose the correct Location AND Group from the drop-downs when adding/deleting availability. (Note: Users often do not choose the correct Group when setting their availability, and this is where an admin can help.)

### Sync Outlook or Google to Appointments

Users can sync Appointments to their Outlook calendars so that their calendars remain in sync when users book appointments with them. There is documentation in Confluence on how to [sync an Outlook or Google Calendar](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/1769498/Setting+Up+Appointments#SettingUpAppointments-SetAvailabilityviaOutlookorGoogle). To check whether someone has sycned their calendar: 

1. In the ```Admin``` drop-down, choose ```Accounts.```
2. Click on the top tab for ```Integration Status.``` There, you will see a green ```Connected``` box under the column for ```Google``` or ```Outlook/Exchange``` if their account has been integrated. 

### Sync Zoom to Appointments 

To use the online meeting option in Appointments, users can sync their Zoom accounts so that Zoom generates an automatic link when users book an appointment, or staff can choose to use their Personal Meeting ID (PMI) instead, with instructions for users to follow. (NOTE: Syncing a Zoom account will also allow users to host online workshops/events via our [Calendars](events.md), so using the method below will ensure that Zoom is integrated across the LibCal tools.)

To sync a Zoom account: 

1. Users should follow the [instructions in Confluence](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/1769545/Integrate+Your+Zoom+Account#IntegrateYourZoomAccount-IntegratingYourZoomAccount), which include contacting our Zoom admins in OIT to enable the integration from the Zoom side. 
2. To check whether an account has been integrated, click on the top tab for ```Integration Status.``` There, you will see a green ```Connected``` box under the column for ```Zoom.```

Each year (around June), users will need to reauthorize Zoom to access their LibCal account. To do this, users can follow the [instructions on Confluence](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/1769545/Integrate+Your+Zoom+Account#IntegrateYourZoomAccount-ReauthorizingYourZoomAccount). 