## LibGuides Look & Feel

LibGuides uses a combination of custom HTML/CSS code and templates to manage the Look & Feel of the overall LibGuides system. PUL has several custom templates, and we keep track of these files on GitHub in the [libapps repo](https://github.com/pulibrary/libapps/tree/main/libguides).   

### Header and Footer

To maintain a consistent header and footer across all LibGuides and across many of our library applications, we use PUL's Lux Design System. To view or update this code:

1. From the ```Admin``` drop-down, choose ```Look & Feel``` from the menu. 
2. In the tab ```Header/Footer/Tabs/Boxes``` you will see drop-downs for the ```Page Header``` and ```Page Footer.``` The code comes from our [Lux Design System repo](https://github.com/pulibrary/lux-design-system), and you can work with the DACS team to edit or update the Guide header/footer using Lux.  

### Custom JS/CSS

Similar to the header and footer, we use custom JavaScript and CSS across our Guides to maintain a consistent look and feel. 

If you want to test how JS/CSS changes will look without altering the overall system, you can use our Testing Group to create test Guides or test changes on that Group's Guide homepage. To do this: 

1. From the ```Admin``` drop-down, choose ```Groups``` from the menu.
2. Search for the Group called ```Testing``` and click the edit icon in the ```Actions``` menu on the right. 
3. Within this Group, you can experiment with header/footer code, JS/CSS code, and Guide page and homepage layouts. You can click the ```View Group Homepage``` button underneath the main navigation to preview any changes you've made to the Testing Group's code. 

To update the overall Guides JS/CSS code: 

1. From the ```Admin``` drop-down, choose ```Look & Feel``` from the menu. 
2. In the tab ```Custom JS/CSS``` you will see drop-downs for the ```Custom JS/CSS Code``` and ```Upload Customization Files.``` 
3. Some of the code in the ```Custom JS/CSS Code``` comes from the [Lux Design System](https://github.com/pulibrary/lux-design-system) while other elements have been added over time. Generally, we don't change this code unless there is a Lux upgrade or we are making accessibility improvements. 
4. We use externally linked CSS customization files to manage other parts of the Guides system. Again, these generally do not get changed unless there is an upgrade or other overall improvement needed. We keep a record of these style sheets in the [libapps repo](https://github.com/pulibrary/libapps/tree/main/libguides). 
5. To update a style sheet, you can download the copy we have on GitHub in the repo's [assets folder](https://github.com/pulibrary/libapps/tree/main/libguides/assets), open a PR to modify it, and once merged, reupload the new file to the ```Customization Files``` area of the Look & Feel by choosing ```Upload New File.``` If you give it the same file name, the new copy will overwrite the previous one. 

### Guides Page Layout

Each individual LibGuide has a layout template, and when a new Guide is created, Guide owners can choose the layout they would like for their LibGuide. We have a few templates that they can choose from, which are maintained by admins in the ```Look & Feel``` section of LibGuides. Our [PUL Best Practices](https://libguides.princeton.edu/guidebestpractices) state that Guides must now use side navigation tabs instead of top tabs for accessibility purposes. Side-Nav Layout templates include the following: 

- System Default - Side-Nav Layout: This has two columns for content
- Side-Nav 3-column-Profile: This has three columns for content, including a box in the 3rd column that includes the Guide creator's profile (other contributors' profiles can be added - see [Confluence documentation](https://pul-confluence.atlassian.net/wiki/spaces/SS/pages/34340875/Add+a+Profile+Box+to+a+LibGuide) on how Guide editors can add Profiles)
- Side-Nav 3-column-no-profile: This has three columns for content but no built-in Profile box; any content box type can be added in this column

To update these template files: 

1. From the ```Admin``` drop-down, choose ```Look & Feel``` from the menu. 
2. In the tab ```Page Layout``` choose the drop-down for ```Guide.```
3. Expand the section called ```Customize Guide Templates``` and select the template you'd like to edit from the drop-down. 
4. If you are making major changes, scroll to the bottom and click the blue button to ```Save as a NEW template``` so that the original template is not lost. If you are making minor changes, you can choose ```Save changes to this template.```
5. Like the CSS customization files discussed above, make sure to find the template you are working with in our [libapps repo](https://github.com/pulibrary/libapps/tree/main/libguides/), open a PR for changes, and save the updated file in the repo to keep the two systems in sync. 

A Guide's layout can also be changed by Guide owners/editors after the Guide has been created: 

1. In a Guide's editing mode, choose the image icon from the top right navigation bar and from the drop-down, select ```Guide Navigation.```
2. Choose the new layout you'd like and hit the blue ```Save``` button. 
3. The new layout may change how the content boxes on the Guide look (sometimes "hiding" them from an old column that is no longer showing). To fix this, choose the ```Page``` drop-down in the gray bar right above the Guide's main conent. 
4. Under ```Reorder / Move``` choose ```Boxes``` and this will show you where current boxes are placed and where you can move them within the Guide.  

#### Guides Homepage Layout (Research Guides Homepage)

The the main [Research Guides](https://libguides.princeton.edu/) homepage linked from the library's website is managed by a custom template, currently called ```Homepage 2020 - Option 2.``` This template is not often updated unless there are major changes. To access or edit this template: 

1. From the ```Admin``` drop-down, choose ```Look & Feel``` from the menu. 
2. In the tab ```Page Layout``` choose the drop-down for ```Homepage.```
3. Expand the section called ```Customize Guide Templates``` and select the template ```Homepage 2020 - Option 2```. 
4. If you are making major changes, scroll to the bottom and click the blue button to ```Save as a NEW template``` so that the original template is not lost. If you are making minor changes, you can choose ```Save changes to this template.```
5. Like the tempalte files discussed above, make sure to find the template in our [libapps repo](https://github.com/pulibrary/libapps/tree/main/libguides/), open a PR for changes, and save the updated file in the repo to keep the two systems in sync. 

The content on the main [Research Guides](https://libguides.princeton.edu/) page is pulled in to the template via a separate LibGuide that hosts content boxes that occasionally need to be updated to due to broken links, changes in Subjects, etc. To update these content boxes: 

1. From the ```Content``` navigation tab in LibGuides, choose ```Guides``` from the drop-down. 
2. In the ```Name``` search box, search for ```Content on Homepage``` and open that Guide. 
3.  You will see the content boxes for the different modules on our Research Guides page and can click the edit icon in the bottom right-hand corner of each box to open the Rich Text Editor and update links, add text, etc. 
