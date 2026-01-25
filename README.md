# Created by ZarScape — If you find this helpful, please leave a ⭐ on the repo!

---

# Discord Components V2 Guide

Discord's **Components V2** system allows you to create rich, interactive, and visually appealing messages entirely with components — no embeds required.  
This guide walks you through the main component types, usage examples, and includes a **full slash command** demonstration.

---

## 1. What Are Components V2?

Components V2 are UI building blocks for Discord messages.  
They allow you to:
- Display formatted text
- Group content into sections
- Add interactive elements like buttons and menus
- Show media galleries
- Attach files
- Organize information with separators

---

## 2. Component Types & Examples

### **TextDisplay**
Static text with Markdown formatting.
```js
const { TextDisplayBuilder } = require('discord.js');
const textDisplay = new TextDisplayBuilder().setContent('📝 **This is a TextDisplay component.**');
````

---

### **Separator**

Visual space or divider between components.

```js
const { SeparatorBuilder, SeparatorSpacingSize } = require('discord.js');
const separator = new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small);
```

---

### **Section**

Groups text, requires a thumbnail or button.

```js
const { SectionBuilder, TextDisplayBuilder, ThumbnailBuilder } = require('discord.js');
const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('📄 **Section Title**'),
    new TextDisplayBuilder().setContent('This is a section description.')
  )
  .setThumbnailAccessory(new ThumbnailBuilder({ media: { url: 'https://example.com/image.png' } }));
```

---

### **Thumbnail**

Small image beside section text.

```js
const { ThumbnailBuilder } = require('discord.js');
const thumbnail = new ThumbnailBuilder({ media: { url: 'https://example.com/avatar.png' } });
```

---

### **Button**

Clickable link or action.

```js
const { ButtonBuilder, ButtonStyle } = require('discord.js');
// Link button
const linkButton = new ButtonBuilder()
  .setLabel('Docs')
  .setURL('https://discord.com/developers/docs/components/overview')
  .setStyle(ButtonStyle.Link);
```

---

### **ChannelSelectMenu**

Dropdown to select a channel.

```js
const { ChannelSelectMenuBuilder } = require('discord.js');
const menu = new ChannelSelectMenuBuilder()
  .setCustomId('channel_select_menu')
  .setPlaceholder('Select a channel…');
```

---

### **MediaGallery**

Carousel of images/videos.

```js
const { MediaGalleryBuilder, MediaGalleryItemBuilder } = require('discord.js');
const gallery = new MediaGalleryBuilder().addItems(
  new MediaGalleryItemBuilder().setURL('https://example.com/image1.png'),
  new MediaGalleryItemBuilder().setURL('https://example.com/image2.png')
);
```

---

### **File**

Attach and reference a file.

```js
const { FileBuilder, AttachmentBuilder } = require('discord.js');
const file = new AttachmentBuilder('./example.json').setName('example.json');
const fileComponent = new FileBuilder().setURL('attachment://example.json');
```

---

### **Container**

Groups multiple components in one.

```js
const { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize } = require('discord.js');
const container = new ContainerBuilder()
  .setAccentColor(0x5865F2)
  .addTextDisplayComponents(new TextDisplayBuilder().setContent('Hello from a container!'))
  .addSeparatorComponents(new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small));
```

---

## 3. Full Slash Command Example

```js
const {
  SlashCommandBuilder,
  MessageFlags,
  TextDisplayBuilder,
  SeparatorBuilder,
  SeparatorSpacingSize,
  ThumbnailBuilder,
  SectionBuilder,
  ChannelSelectMenuBuilder,
  ActionRowBuilder,
  ContainerBuilder,
  MediaGalleryBuilder,
  MediaGalleryItemBuilder,
  ButtonBuilder,
  ButtonStyle,
  FileBuilder,
  AttachmentBuilder
} = require('discord.js');
const path = require('path');
const config = require('../../config/config.json');

module.exports = {
  data: new SlashCommandBuilder()
    .setName('v2-components')
    .setDescription('Demonstrates all V2 components'),
  async execute(interaction, client) {
    const botAvatar = client.user.displayAvatarURL({ extension: 'png', size: 512 });

    const textDisplay = new TextDisplayBuilder().setContent('🔹 TextDisplay example');
    const separator = new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small);

    const sectionThumb = new SectionBuilder()
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('📄 **Section Title**'),
        new TextDisplayBuilder().setContent('Description with thumbnail.')
      )
      .setThumbnailAccessory(new ThumbnailBuilder({ media: { url: botAvatar } }));

    const selectMenu = new ActionRowBuilder().addComponents(
      new ChannelSelectMenuBuilder().setCustomId('channel_select').setPlaceholder('Select a channel…')
    );

    const mediaGallery = new MediaGalleryBuilder().addItems(
      new MediaGalleryItemBuilder().setURL('https://example.com/image1.png'),
      new MediaGalleryItemBuilder().setURL('https://example.com/image2.png')
    );

    const sectionButtons = [
      new SectionBuilder().addTextDisplayComponents(new TextDisplayBuilder().setContent('🔗 **Docs**'))
        .setButtonAccessory(new ButtonBuilder().setLabel('Overview').setURL('https://discord.com/developers/docs/components/overview').setStyle(ButtonStyle.Link)),
      new SectionBuilder().addTextDisplayComponents(new TextDisplayBuilder().setContent('📑 **Reference**'))
        .setButtonAccessory(new ButtonBuilder().setLabel('Types').setURL('https://discord.com/developers/docs/components/reference#what-is-a-component-component-types').setStyle(ButtonStyle.Link)),
      new SectionBuilder().addTextDisplayComponents(new TextDisplayBuilder().setContent('🚀 **Getting Started**'))
        .setButtonAccessory(new ButtonBuilder().setLabel('Guide').setURL('https://discord.com/developers/docs/components/using-message-components').setStyle(ButtonStyle.Link))
    ];

    const filePath = path.join(__dirname, '../../assets/embed-export.json');
    const attachment = new AttachmentBuilder(filePath).setName('embed-export.json');
    const fileComponent = new FileBuilder().setURL('attachment://embed-export.json');

    const container = new ContainerBuilder()
      .setAccentColor(parseInt(config.color.replace('#', ''), 16))
      .addMediaGalleryComponents(mediaGallery)
      .addSectionComponents(sectionThumb)
      .addMediaGalleryComponents(new MediaGalleryBuilder().addItems(new MediaGalleryItemBuilder().setURL(botAvatar)))
      .addSectionComponents(...sectionButtons)
      .addSeparatorComponents(new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small))
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('📝 **Fully composed with Components V2**'),
        new TextDisplayBuilder().setContent('- TextDisplay: static text'),
        new TextDisplayBuilder().setContent('- Section: grouped text/accessories'),
        new TextDisplayBuilder().setContent('- MediaGallery: images'),
        new TextDisplayBuilder().setContent('- Separator: content dividers'),
        new TextDisplayBuilder().setContent('- File: attachments'),
        new TextDisplayBuilder().setContent('- Button: actions/links'),
        new TextDisplayBuilder().setContent('- ChannelSelectMenu: choose channels')
      )
      .addFileComponents(fileComponent);

    await interaction.reply({
      flags: MessageFlags.IsComponentsV2,
      components: [textDisplay, separator, sectionThumb, selectMenu, container],
      files: [attachment]
    });
  }
};
```

---

## 4. Best Practices

* Group related items in containers for structure.
* Use separators for better readability.
* Keep text short for mobile users.
* Use buttons for quick links and actions.
* Ensure all URLs are valid and accessible.
* Reference attached files using `attachment://filename`.

---

# Example Previews:

![ExamplePreview1](https://repository-images.githubusercontent.com/1038141980/c0761e9e-aebb-4ed6-885c-f74a30ffc3fd)

---


## 5. Resources

* [Discord Developer Docs — Components Overview](https://discord.com/developers/docs/components/overview)
* [Component Types Reference](https://discord.com/developers/docs/components/reference#what-is-a-component-component-types)
* [Using Message Components](https://discord.com/developers/docs/components/using-message-components)
