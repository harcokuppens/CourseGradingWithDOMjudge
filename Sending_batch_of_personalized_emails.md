    
# Sending a batch of personalized emails

The general method to send personalized emails, is to use a template with variables that are replaced with specific values for each recipient. These values can be stored in a simple `.csv` file, with each row corresponding to a recipient. The first row in the .csv file is a special row, also called the header row, which specifies the variable name for each column. All other rows are receiver data.  This `.csv` file can be easily parsed to fill the template for each user.

There are two main methods to send personalized emails using data from a .csv file:

1. Scripting the generation of `.eml` files yourself and then sending these files with a **mail command-line tool**.
2. Using specialized **mail merge** software.

## Scripting the generation of `.eml` files from a `.csv file`, then send .eml files


* first generate `.eml` files from `.csv` file using a script

* then send `.eml` files
    * send using mail script, or  
        
    * use Thunderbird 
        * first install the **ImportExportTools NG** extension
        * then right click on `Outbox` folder in `Local Folders`
        * in upcoming popup menu select

                "ImportExportTools NG" -> Import EML Messages -> Individual EML Messages
        
        * in upcoming file browser select all `.eml` files you want to send
            and confirm
        * the selected elm files should now be listed in the `Outbox` folder 
        * then in thunderbird select the menu entry   

                File -> Send Unsent Messages
          
          which causes all files in the  `Outbox` folder  to be send
     

## Use a "mail merge" tool with data from .csv file directly:
    

Below we refer 2 "mail merge" tools, where **Thunderbird** with the `mail merge` extension works the best.

   * using **Thunderbird** with the `mail merge` extension
       https://addons.thunderbird.net/thunderbird/addon/mail-merge/
    
   * using `mail merge` tooling builtin **Word** on Windows or MacOS.
      This works best if you make Outlook your default email application.
      see article "use mail merge to send bulk email messages"
       https://support.microsoft.com/en-us/office/use-mail-merge-to-send-bulk-email-messages-0f123521-20ce-4aa8-8b62-ac211dedefa4
        The article claims that generated emails will be put in "Outbox", from which you can easily then send them all at once. However in my test
        it showed that emails were directly send. You can still put then in the "Outbox" by putting your email offline. You can send them all at once by putting
        your email program online again. If you use Thunderbird as your default email application then the emails are even in offline mode still directly send. 
        So Outlook works better in this case.
         
 **IMPORTANT:** using multiple email adresses comma separated in single email field in csv file does not work with `mail merge` tool builtin
        into Word. This however works fine with the  Thunderbird with the `mail merge` extension. 
        Word does not support this, causing the malformed emails to get stuck in the Drafts folder. The only way that you can do it using Word's mail merge facility is to have a record 
        for each email address in the data source.