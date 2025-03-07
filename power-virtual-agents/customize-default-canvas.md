---
title: "Customize the web chat canvas"
description: "Change the name, icon, and color of your bot with CSS and JavaScript styling."
keywords: "PVA"
ms.date: 04/19/2022

ms.topic: article
author: iaanw
ms.author: iawilt
ms.custom: "customization, ce06102020"
ms.service: power-virtual-agents
ms.collection: virtual-agent
---

# Customize the look and feel of the bot's default canvas

[!INCLUDE[public preview disclaimer](includes/public-preview-disclaimer-prod.md)]

Select the version of Power Virtual Agents you're using here:

> [!div class="op_single_selector"]
>
> - [Power Virtual Agents web app](customize-default-canvas.md)
> - [Power Virtual Agents app in Microsoft Teams](teams/customize-default-canvas-teams.md)

After you create and [publish a bot](publication-fundamentals-publish-channels.md), your customers can [use the bot's Web Chat canvas to interact with it](publication-connect-bot-to-web-channels.md).

The default look and feel of the bot is defined by the bot's canvas. You can customize the canvas in two ways, depending on the complexity of the customizations:

1. You can [customize the default canvas](#customize-the-default-canvas-simple) with JavaScript-based styling in the HTML code for the website where you deploy your bot.  
    Customizing the default canvas is useful if you want to make small customizations without investing in code development.

1. You can use a [custom canvas](#customize-and-host-your-chat-canvas-advanced), based on the [Bot Framework Web Chat canvas](https://github.com/microsoft/BotFramework-WebChat).  
    Connecting to a custom canvas requires extensive developer knowledge and is useful for organizations that want to customize the experience completely.

You can also combine the customized canvas with [configuring your bot to automatically start the conversation](configure-bot-greeting.md).

Lastly, you can [change the name and bot icon](#change-the-bot-name-and-icon) (when the bot is [shared in Microsoft Teams](publication-add-bot-to-microsoft-teams.md)) directly from the portal.

## Prerequisites

- [Learn more about what you can do with Power Virtual Agents](fundamentals-what-is-power-virtual-agents.md).

> [!IMPORTANT]
> You may install and use the sample code included in this documentation only for use with the Microsoft Power Virtual Agents product. The sample code is licensed "as is" and is excluded from any service level agreements or support services. You bear the risk of using it.  
>
> Microsoft gives no express warranties, guarantees, or conditions and excludes all implied warranties, including merchantability, fitness for a particular purpose, and non-infringement.

## Change the bot name and icon

> [!IMPORTANT]
> If your bot is connected to [Omnichannel for Customer Service](configuration-hand-off-omnichannel.md), the bot's name is defined by the **Display name** property in the Azure portal registration.

You can change the bot's name and icon. This will affect the icon in all channels where you publish your bot.

1. In the navigation menu, under **Settings**, select **Details**.

1. Change the bot's name and icon. [Review the recommendations on Microsoft Teams icon formats](/microsoftteams/platform/resources/schema/manifest-schema#icons)

1. Select **Save** to commit your changes.

    :::image type="content" source="media/customize-default-canvas/custom-canvas-name-icon.png" alt-text="The bot details pane lets you change the name and icon.":::

> [!IMPORTANT]
> After updating your bot's icon, it may take up to 24 hours for the new icon to appear everywhere.

## Retrieve token endpoint

To customize your canvas, whether it's the default canvas or a custom one you connect to, you need to retrieve your bot details.

1. In the navigation menu under **Settings**, select **Channels**.

1. Select **Mobile app**.

    :::image type="content" source="media/customize-default-canvas/channel-mobile-app.png" alt-text="Screenshot of the mobile app channel tile.":::

1. Next to **Token Endpoint**, select **Copy**.

    :::image type="content" source="media/customize-default-canvas/token-endpoint.png" alt-text="Screenshot of the endpoint token id.":::

## Customize the default canvas (simple)

Configure how the chat canvas looks with some simple CSS and JavaScript styling options.

First, you need to configure where you're deploying your bot canvas.

1. [Create and publish a bot](fundamentals-get-started.md).

1. Copy and paste the HTML code below and save it as _index.html_.  
    You can also copy and paste the code below into the [w3schools.com HTML try it editor](https://www.w3schools.com/html/tryit.asp?filename=tryhtml_default). You'll still need to add your token endpoint.  

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Contoso Sample Web Chat</title> 
        <!-- This styling is for the Web Chat demonstration purposes. It is recommended that style is moved to separate file for organization in larger projects -->
        <style>
            html, body {
                height: 100%;
            }

            body {
                margin: 0;
            }

            h1 {
                font-size: 16px;
                font-family: Segoe UI;
                line-height: 20px;
                color: whitesmoke;
                display: table-cell;
                padding: 13px 0px 0px 20px;
            }

            #heading {
                background-color: black;
                height: 50px;
            }

            .main {
                margin: 18px;
                border-radius: 4px;
            }

            div[role="form"]{
                background-color: black;
            }

            #webchat {
                position: fixed;
                height: calc(100% - 50px);
                width: 100%;
                top: 50px;
                overflow: hidden;
            }

        </style>

    </head>
    <body>
        <div>
            <div id="heading">
                <!-- Change the h1 text to change the bot name -->    
                <h1>Contoso Bot Name</h1>
            </div>
            <div id="webchat" role="main"></div>
        </div>    

      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>

      <script>
            const styleOptions = {

               // Add styleOptions to customize Web Chat canvas
               hideUploadButton: true
            };

            
            var theURL = "<YOUR TOKEN ENDPOINT>";
          
            var environmentEndPoint = theURL.slice(0,theURL.indexOf('/powervirtualagents'));
            var apiVersion = theURL.slice(theURL.indexOf('api-version')).split('=')[1];
            var regionalChannelSettingsURL = `${environmentEndPoint}/powervirtualagents/regionalchannelsettings?api-version=${apiVersion}`; 
            
            var directline;
                fetch(regionalChannelSettingsURL)
                    .then((response) => {
                        return response.json();
                        })
                    .then((data) => {
                        directline = data.channelUrlsById.directline;
                        })
                    .catch(err => console.error("An error occurred: " + err));
        
          fetch(theURL)
                .then(response => response.json())
                .then(conversationInfo => {
                    window.WebChat.renderWebChat(
                        {
                            directLine: window.WebChat.createDirectLine({
                                domain: `${directline}v3/directline`,
                                token: conversationInfo.token,
                            }),
                            styleOptions
                        },
                        document.getElementById('webchat')
                    );
                })
                .catch(err => console.error("An error occurred: " + err));

        </script>
      </body>
    </html>
    ```

1. In the _index.html_ file you created, enter your token endpoint at the line `var theURL = "<YOUR TOKEN ENDPOINT>";`.

1. Open _index.html_ using a modern browser (for example, Microsoft Edge) to open the bot in the custom canvas.

1. Test the bot to ensure you are receiving responses from your bot and that it's working correctly.  

    If you encounter problems, make sure you've published your bot, and that your token endpoint has been inserted in the correct place. It should be after the equals sign (=) at the line `var theURL = "<YOUR TOKEN ENDPOINT>"`, and surrounded by double quotation marks (").

### Customize the bot icon, background color, and name

Once you get the customized canvas working with your bot, you can make changes to it.

You can use the JavaScript `styleOptions` options to configure a number of pre-defined styles.

See [Web Chat customization](/azure/bot-service/bot-builder-webchat-customization?view=azure-bot-service-4.0&preserve-view=true) for links to the defaultStyleOptions.js file and more information on what you can customize and how it will look.

#### Change the bot icon

1. Update the _index.html_ file with the following sample code:

    ```js
    const styleOptions = {
                botAvatarInitials: 'BT',
                accent: '#00809d',
                botAvatarBackgroundColor: "#FFFFFF",
                botAvatarImage: 'https://learn.microsoft.com/azure/bot-service/v4sdk/media/logo_bot.svg',
                userAvatarImage: 'https://avatars.githubusercontent.com/u/661465'
            };  
    ```

1. Replace the bot and user avatar images with your company images.  
    If you don't have an image URL, you can use a Base64-encoded image string instead.

#### Change the background color

1. Update the _index.html_ file with following sample code:

    ```js
    const styleOptions = {
           
                backgroundColor: 'lightgray'

            };  
    ```

1. Change `backgroundColor` to any color you wish. You can use standard CSS color names, RGB values, or HEX.

#### Change the bot name

1. Update the `<h1>` text in the _index.html_ file with the following:

    ```HTML
    <body>
        <div id="heading">
             <!-- Change the h1 text to change the bot name -->
             <h1><img src="contosobot-teams.png"> Contoso Bot Name</h1>
        </div>

    ```

1. Change the text to whatever you want to call the bot. You can also insert an image, although you may need to style it to ensure it fits within the heading section.

## Customize and host your chat canvas (advanced)

You can connect your Power Virtual Agents bot with a custom canvas that is hosted as a standalone web app. This option is best if you need to embed a customized iFrame across multiple web pages.

> [!NOTE]
> Hosting a custom canvas requires software development. Our guidance here is intended for experienced IT professionals, such as IT admins or developers who have a good understanding of developer tools, utilities, and IDEs.

### Pick a sample to customize

We recommend starting with one of these samples custom-built to work with Power Virtual Agents:

- [Full bundle](https://github.com/microsoft/PowerVirtualAgentsSamples/tree/master/BuildYourOwnCanvasSamples/1.starter-full-bundle) is a custom canvas capable of showing all rich content from Power Virtual Agents. For example:

  :::image type="content" source="media/customize-default-canvas/custom-canvas-full-bundle.png" alt-text="Full bundle custom canvas." border="false":::

- [Location and file uploading](https://github.com/microsoft/PowerVirtualAgentsSamples/tree/master/BuildYourOwnCanvasSamples/2.location-and-file-uploading) is a custom canvas capable of getting a user's location and sending it to a bot using Power Virtual Agents. For example:

  :::image type="content" source="media/customize-default-canvas/custom-canvas-location-file-upload.png" alt-text="Location and file uploading custom canvas." border="false":::

Or you can pick from [other sample web chat canvases](https://github.com/microsoft/BotFramework-WebChat/#samples-list) provided by Bot Framework.

### Customize canvas using `stylesetOptions`

As with customizing the default canvas, you can use `styleSetOptions` to customize the custom canvas. All customizable properties are listed in [defaultStyleOptions.js](https://github.com/microsoft/BotFramework-WebChat/blob/main/packages/api/src/defaultStyleOptions.ts). For more information on what you can customize and how it will look, see [Web Chat customization](/azure/bot-service/bot-builder-webchat-customization?view=azure-bot-service-4.0&preserve-view=true).

### Deploy your customized canvas

In order to host your custom canvas, deploy all files to a web app.

## See also

- [Bot Framework web chat](https://github.com/microsoft/BotFramework-WebChat)
- [Generating a direct line token](/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?preserve-view=true&view=azure-bot-service-4.0#generate-token)

[!INCLUDE[footer-include](includes/footer-banner.md)]
