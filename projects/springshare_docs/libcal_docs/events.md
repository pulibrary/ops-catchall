## LibCal Events

The LibCal Events module hosts our PUL event calendars, repository of event registration forms, and calendar widgets that are embedded in various library applications (PUL website, Confluence, LibGuides, etc.). We have four main calendars in our Events; their purposes, in addition to different account levels, are explained for users on our [LibCal Confluence page](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/1769508/Event+Calendars). Below will list some common admin tasks in Events:   

### Manage Calendar Permissions

Each calendar has its own settings and permissions, which can be edited in the ```Calendar Settings``` tab at the top of any calendar. By default, LibCal admins can see/edit all calendars. LibCal regular users are added as Editors to calendars, meaning that they can create/edit their own events only. To change these permissions:

1. In the ```Calendar Settings``` tab of any calendar, scroll to the bottom and expand the panel that says ```Regular User Custom Access.```
2. You can edit any existing user's permissions by clicking the editing icon in the ```Action``` column to the right of a user's name. When you edit, you will see the permissions options available. 
3. To add a user to this calendar, click the blue ```Add Users``` button at the top of this section and set their permissions. 

### Manage Calendar Categories 

Categories can be added to events on our calendars to make them filterable for users on the main library website [calendar page](https://libcal.princeton.edu/calendar/events). You can also send a link from a filtered category to show users only events in that category. Only admins in LibCal can manage categories at the Calendar level. To do this: 

1. Open any calendar and click on the ```Categories``` tab in the top navigation
(Note: each calendar has different categories and you'll want to check with the calendar owner before making any changes).  
3. To edit an existing Top Level Category, click the pencil/paper icon in the ```Action``` column to the right of the category. You can change the name and color of the category there. 
4. Any category can also have a Second Level Category. To add one, click the plus icon in the ```Action``` column to the right of the category.

### Manage Calendar Internal Tags

Internal tags are used mainly by staff for internal and tracking purposes and unlike categories, internal tags do not show up on the user-facing side of calendars. However, you can embed a calendar as a widget, RSS feed, or iCal subscription using internal tags, so they are useful in this way as well. Internal tags are available system-wide and can be used for an event on any of our calendars. To view/edit tags:  

1. In the ```Admin``` drop-down, choose ```Events.```
2. Choose the ```Internal Tags``` tab in the top navigation. 
3. You can add a new internal tag by clicking the blue ```Add New Internal Tag``` button at the top. 
4. You can edit the name of any internal tag by clicking the pencil/paper icon in the ```Action``` column to the right of the tag.

### Event Registration Forms

Registration forms can be customized for specific events. By default, if no registration form is chosen for an event, LibCal will default to a form that includes a registrant's Full Name and Email. To create or customize registration forms: 

1. In LibCal, click on the ```Events``` top menu item and choose the tab called ```Registration Forms.```
2. Here, there is the ability to ```Create New Form,``` ```Copy existing Form,``` or you can modify a form that we already have in the list. 
3. Once your form has been saved, you can enable it when creating a new event. As you are creating the event, scroll down to the section on ```Event Registration``` and check the box for ```Registration is required.``` Choose the form you would like from the ```Registration Form``` drop-down. 
4. Note that with Registration Forms, the default is set so that users must authenticate with CAS in order to register for an event. To turn this off, choose ```Inactive``` from the ```Activate LibAuth Authentication``` drop-down. 

### Embed/Export a Calendar

Often, folks will want to embed an event calendar in a LibGuide, on a Confluence page, or on our Library Website. To do this: 

1. Open the calendar in LibCal that you want to embed, and click on the ```Embed/Export``` tab at the top. 
2. There are options to embed an iCal Subscription (used for our Library Website), XML Export, RSS Feeds, and Widgets (note: widgets are accessed in a different secion of LibCal that links from this section). 
3. Each way of exporting/embedding allows you to choose how you would like the calendar to appear and how it should be filtered (e.g. by category, audience, internal tag, etc.).
4. Once you choose the options mentioned above, LibCal will generate a link or emebed code that you can use in any other web interface that accepts links/code. 