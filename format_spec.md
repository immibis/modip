# MODIP Format Specification

This document provides detailed information on the MODIP Format.

The MODIP format uses JSON to store information. If providing an API that returns format-compliant information, the `application/json` Content-Type header SHOULD be provided. If stored on disk, the `.modip.json` extension SHOULD be used. For storage on disk of modpacks/instances, see **multifile_zips.md**.

A format-compliant example is provided in **examples/format_example.modip.json**.

## Fields

Fields are stored inside the JSON metadata of a Project. Fields that are optional will be marked as such. All fields are stored as a String value unless stated otherwise. String fields are subject to the `Banned Characters` list located in **format_values.md** unless otherwise noted. Below is a complete list of all fields available for the MODIP Format, grouped into categories.

## General Project Information

### `id`

The ID of the Project. There is no standard or required format on how to store an ID, other than it CANNOT include spaces. It may be a slug, UUID, random number, or other method of storing IDs. It is RECOMMENDED that IDs are not tied to the name of a project.

This field may contain any printable ASCII character except for spaces (U+0021 to U+007E, inclusive)

---

### `name`

The name of a Project. This name MUST be a human readable name and SHOULD NOT contain unnecessary information. For example, the name field should not specify where the project is being hosted, or the authors of a project.

---

### `summary` (optional)

A short, one sentence summary of the Project. Formatted Text CANNOT be used. This may be displayed in search results, for example.

---

## Detailed Project Information

### `description` (optional)

A long description of the project. Formatted Text MAY be used.

---

### `media` (optional)

This field MUST be stored as an array. This contains Media Item objects, which have the following fields.

##### `rel`

The relation of this Media Item. Valid options are:

- `icon` - This is the main icon of this project.
- `gallery` - This item should be shown in a list of other items marked `gallery`.

This field CAN be an array of multiple values. Currently this is not needed, however it exists so more `rel` values can be added later.

The `icon` relation CAN ONLY be present in ONE Media Item. It CANNOT be present in multiple Media Items. It is NOT required.

The `gallery` relation CAN be present in multiple Media Items.

If an item with the `icon` relation is not present, launchers should show a placeholder image.

##### `type`

The type of this Media Item. Valid options are `video` and `image`. This field exists to indicate to launchers what this item is and to allow them to render correctly. Launchers SHOULD NOT infer type based on file extension.

##### `embed`

The `embed` field is valid for both `video` and `image`. It contains a URI to a webpage which SHOULD be embedded when displaying this Media Item. It is recommended for hosts to only allow trustworthy domains to be embedded.

There is no difference between embedded videos and images, as they both display webpages, however the type field MUST still be specified.

##### `src`

The `src` field is valid for both `video` and `image`. It contains a direct URI to either a playable video file or an image file. It is RECOMMENDED for images to be in the WebP or PNG formats, and it is RECOMMENDED for videos to be in the WebM format. Recommended video codecs include VP9 and AV1.

`data` URIs are allowed, but are not recommended because they increase the file size substantially.

**`embed` and `src` are conflicting fields and both fields CANNOT be present in the same Media Item.**

TODO: can src be a list?

##### `caption` (optional)

An optional string which can be displayed as a caption for the current Media Item. Formatted Text CANNOT be used. `caption` CANNOT be present when `rel` is set to `icon`.

##### `sha256` (optional)

An optional SHA256 checksum for the media file. This field CANNOT be present if the `embed` field is present.

---

### `authors` (optional)

This field MUST be stored as an array. It contains objects storing the information about each author. Those objects MUST be EITHER a Team or an individual Author.

**Team Objects consist of the following fields:**

##### `team`

Teams MUST have a `team` field that is set to true. A `team` field that is not set to `true` is invalid.

##### `id`

A unique identifier to this team, allowing you to search by Team. There is no required format for how this ID should be generated, however it MUST be unique to the Team.

##### `name`

The human readable name of the team. Spaces are allowed.

##### `uri` (optional)

An optional field that can link to a webpage relating to the Team.

##### `members`

An array containing Author objects, who are the members of the team.

**Author Objects consist of the following fields:**

##### `id`

A unique identifier to this author. The same requirements for Team IDs should be followed here. This ID MUST be unique to the author.

##### `name`

The human readable name of the author. Spaces are allowed.

##### `uri` (optional)

An optional field that specifies a website relating to the author. It may be the author's personal website, the author's profile on a certain host, or any valid URL.

---

### `tags` (optional)

This field MUST be stored as an array of strings. Tags contain grouping information about the project - sometimes referred to as "categories" by other hosts. Example tags include `mod`, `modpack`, `framework`, `magic`, and `tech`. Standardized tag names are available in **format_values.md**. The standard SHOULD be followed, however it DOES NOT have to be followed.

If other information is useful for a tag, such as an icon or URL, this metadata should be provided by the host. There is currently no standardized method of defining this information.

---

### `license` (optional)

This is an object that contains information about the license that a project is under. It contains the following fields:

##### `id`

The identifier of the license. Valid options are:

- [All SPDX License Identifiers](https://spdx.org/licenses/)
- `ARR` - All Rights Reserved
- `MMPL-1.0` - Minecraft Mod Public License
- `Generic-Open` - An generic open source license
- `Generic-Proprietary` - An generic proprietary license

Use the `Generic-Open` and `Generic-Proprietary` IDs for when a Project is using a custom license or a license not specified in the list above.

##### `name`

The human readable name of the license. It is recommended to keep it short and similar to the identifier specified in `id`. For identifiers users may not be familiar with, it is recommended to expand them. For example, use `All Rights Reserved` instead of `ARR`. Similarly, use `Minecraft Mod Public License` instead of `MMPL`.

If using the `Generic-Open` or `Generic-Proprietary` IDs, use the actual name of the license for this field. If the license is specific to the Project, it's recommended to use `Custom License` as the name.

##### `uri`

A URL linking to a readable copy of the license.

##### `modpackUsage` (optional)

A field describing usage of the Project in modpacks. Valid options:

- `alwaysAllow` - This Project can be used in a modpack without asking for permission.
- `requiresPermission` - Before included in a modpack, permission must be granted from the author.
- `neverAllow` - This Project can never be used in a modpack.

This field overrides any restrictions stated in the Project's license. If you are a mod author and set this field to `alwaysAllow`, you are granting permission for the files linked in this metadata to be redistributed inside a modpack.

If you are unsure whether or not you want your mod to be distributed in modpacks, `requiresPermission` is recommended.

If this field is not specified, launchers should assume `requiresPermission`.

---

### `links` (optional)

This field MUST be stored as an array. It contains a collection of links relating to the Project. Each Link Object stored inside this arrow MUST contain the following fields:

##### `rel`

This field MUST be stored as an array OR a String. If stored as an array, it MUST contain Strings. This field contains information about what the link is. Valid standardized values are:

- `homepage` - The official homepage of a Project
- `documentation` - Documentation of a Project.
- `wiki` - Wiki information about a Project.
- `scm` - The source code of a Project, for example a GitHub URL.
- `issues` - An issue tracker where bugs and feature requests can be filed.
- `community` - A forum or other online discussion platform where users can discuss the Project.

Other values MAY be used, such as `bitbucket` or `github`. However, it is RECOMMENDED to use generic terminology instead of specific names. If you are unsure whether a value COULD become standardized, please prefix it with `x-`.

##### `name`

A human-readable name of the link. For example, "Source Code" or "Project Website".

##### `uri`

The link, to be viewed in a web browser.

---

### `successors` and `predecessors` (optional)

If an older or newer version of a Project exists, but has a separate ID, these fields may be used. These fields MUST be an Array containing the respective project's IDs and optionally `src` fields. The Projects should be listed in chronological order from when they were first released.

```jsonc
"successors": [
  {
    "id": "first-successor"
  },
  {
    "id": "second-successor",
    "src": "https://example.com/second-successor.modip.json"
    // This successor is hosted externally, and not on the same host as the parent project
    // See the `src` field in dependencies for more info.
  }
]
```

In the example above, the project `first-successor` was released first. After the release of `first-successor`, the project `second-successor` was released.

The successor and predecessor projects should, ideally, be the same project, just under a different ID. For example, the project may have been forked and an updated version was created by a different person.

---

## Version Information

Projects can contain a `versions` array, which lists Version Objects. These objects are stored in chronological order from most recent to least recent.

## Version Objects

Version Objects contain fields relating to a certain version of a Project. Below are all the fields that a Version Object may contain.

### `id`

The ID of this version. There is no required format for how to format a version ID, other than that they CANNOT include spaces.

---

### `name`

The human-readable name of this version. It is RECOMMENDED to follow Semantic Versioning, but it is not required. Spaces MAY be used in this name.

---

### `semver` (optional)

An optional Semantic Versioning compatible version number. It is HIGHLY RECOMMENDED that this field only be present when a version's name conforms to SemVer. This field CAN be present when the version's name does not conform to SemVer, however it's recommended to only be present if the name conforms.

Launchers can use this field to compare different versions. However, because many Minecraft mods do not conform to SemVer, it's recommended for launchers to compare using other fields, such as `releaseDate`.

---

### `releaseDate`

The release date of this version. It MUST be stored as an ISO-8601 conforming String. This MUST include UTC time at the end. A valid example is `2020-01-01T12:00:00Z`. This example date is the 1st of January, 2020 at 12:00:00 UTC. Spaces and time zone offsets (e.g. `+01:00`) CANNOT be used. The `Z` at the end of the string MUST be included and it MUST be capitalized. The `T` separating the date and time MUST be included and it MUST be capitalized. Other values, such as `2020-W32` are NOT allowed. The ONLY allowed format is the one demonstrated in the example.

---

### `changelog`

The changelog of this version. The changelog MAY contain Formatted Text. It follows the same rules as `description` for what is allowed.

---

### `allowed` (optional)

Whether or not this version is allowed to be installed, based on the condition. **This field MUST be a condition, with a boolean value**

---

### `files`

This field contains the downloadable files of a Version. It MUST be an array of File Objects.
File Objects contain information on a downloadable file. Fields are as follows:

##### `name`

There is no restriction on what this value can be. However, it is RECOMMENDED that this be the name of the file. Launchers and other automation tools MAY ignore the value. This value CAN be shown to users.

#### `summary`

A short label for the file. This is optional.
If the purpose of the file is not obvious from the name and rel, this may help the user understand it. E.g. "obfuscated source code".
If present, it should be displayed after the filename in the file list.
Inline formatted text can be used.

##### `sha256`

An SHA256 checksum of the file. This may be used by launchers in order to verify download integrity.

##### `rel`

The relation of this file. Possible values for this field are available in **format_values.md**. The values of this field CAN be present in multiple files. For example, a version can have a file for both the Forge and Fabric modloaders, and both of those files can have the `rel` field set to `primary`.

##### `downloads`

This field MUST be an array containing a download source of the file, stored as a String. The download sources go from highest priority to lowest priority inside the array. For example:

```
"downloads": [
  "https://firsthostingsite.com/example.jar",
  "https://secondhostingsite.com/example.jar
]
```

If a launcher was trying to download the file, it would first try at `firsthostingsite.com`. If it was unable to download from there, it would then try from `secondhostingsite.com`.

---

## Conditions

Conditions allow values to be applied based on variables. Conditions can only be used on certain fields which explicitly allow them. 

The most obvious use of Conditions is for specifying client and server side mods. A modpack, for example, can use conditions to exclude client-only mods from the server. Below is a basic example of that:

```jsonc
// This would go inside a version object in a modpack
"dependencies": {
  {
    // This mod is used for both client and server
    "id": "common-mod",
    "required": true,
    ...
  },
  {
    "id": "client-mod",
    "allowed": {
      "environment": {
        "client": true,
        "server": false
      }
    }, // This mod is allowed *only* on a client
    "required": true // This mod is required for clients; for servers, it cannot be installed
  }
}
```

See **format_values.md** for the list of conditions and groups.

For information on how to implement conditions in a launcher, see **format_implementing.md**.

# Security considerations

A malicious project description may contain a link to, for example, `http://192.168.1.1/cgi-bin/ResetToFactoryDefaults?confirm=yes` (for example, as a download URL) - clicking on this link could (depending on the router model) reset your home router. This is an Internet-wide problem - not specific to MODIP - which could be solved by the router vendor by requiring a CSRF token, or by requiring the HTTP POST method. Systems which download files:

* MAY blacklist RFC1918 and RFC3927 IP addresses, IPv6 link-local addresses, and so on.
* MAY blacklist certain URL schemes.
* MUST NOT blacklist the `http` and `https` URL schemes.
* MUST NOT blacklist port numbers. (Sensitive ports like 25 should already be blocked by the user's browser)
* SHOULD blacklist the `file` URL scheme (however, some systems may have compelling reasons not to). [Note: This differs from the hyperlink recommendations in formatted_text.md]

[a similar paragraph also appears in formatted_text.md - should we combine these and put it in security_considerations.md?]
