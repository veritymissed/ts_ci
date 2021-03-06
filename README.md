<p align="center">
    <img src="https://user-images.githubusercontent.com/6702424/82094662-cd17c200-96fd-11ea-8645-808344bad951.png">  
</p>
<p align="center">
    <i> A template to assist you in creating and publishing TypeScript modules.</i>
    <br>
    <br>
</p>

---


# Presentation 

This template automates the boring and tedious tasks of:
- Filling up the ``package.json``
- Setting up Typescript.
- Writing a [README.md](https://github.com/garronej/denoify_ci/blob/dev/README.template.md) with decent presentation and instructions on how to install/import your module.
- Testing on multiple Node version before publishing.
- Maintaining a CHANGELOG.
- Publishing on NPM and creating corresponding GitHub releases.

Besides, good stuff that comes with using this template:
- No source files are tracked on the ``master`` branch.
- Shorter specific file import path.  
  ``import {...} from "my_module/theFile"`` instead of the usual
  ``import {...} from "my_module/dist/theFile"`` 
- CDN distribution for importing from an ``.html`` file with a ``<script>`` tag.
- A branch ``latest`` always in sync with the latest release.

If you want your module to support Deno as well checkout [denoify_ci](https://github.com/garronej/denoify_ci).

# How to use

## Fork it ( click use the template )

- Click on *Use this template*
- The repo name you will choose will be used as a module name for NPM so:
  - Be sure it makes for a valid NPM module name.
  - Check if there is not already a NPM module named like that.
- The description you provide will be the one used on NPM and in ``package.json`` ( you can change it later )

Once you've done that a GitHub action workflow will set up the ``README.md`` and the ``package.json`` for you, wait a couple of minutes for it to complete ( a bot will push ). You can follow the job advancement in the "Action" tab.

Each time you will push changes ``npm test`` will be run on remote docker containers against multiple node versions if everything passes you will get a green ``ci`` badges in your readme.

## Enable automatic publishing

Once you are ready to make your package available on NPM you 
will need to provide two tokens so that the workflow can publish on your behalf:

Go to repository ``Settings`` tab, then ``Secrets`` you will need to add two new secrets:
- ``NPM_TOKEN``, you NPM authorization token.
- ``PAT``, GitHub **P**ersonal **A**ccess **T**oken with the **repo** authorization. [link](https://github.com/settings/tokens)

To trigger publishing edit the ``package.json`` ``version`` field ( ``0.0.0``-> ``0.0.1`` for example) then push changes... that's all !
The publishing will actually be performed only if ``npm test`` passes.  

# Few things you need to be aware of before getting started

- You probably want to "Use this template" ( the green button ) instead of forking the repo.  
- The files to include in the NPM bundle are cherry-picked using the ``package.json`` ``files`` field.  
  If you don't want to bother and includes everything just remove the ``files`` field from the ``package.json``
  otherwise remember, when you add a subdirectory in ``src/``, to update the ``package.json`` ``files``.
- If you are going to programmatically load files outside of the ``dis/`` directory ( like the ``package.json`` or files inside ``res/`` ) be mindful that the paths might not be the one you expect. [Details](#accessing-files-outside-the-dist-directory). 
- The template does not support ``.npmignore`` ( it use the safer ``package.json`` ``files`` instead ).
- The template does not support ``.npmrc``.
- In rare occasions the workflow in charge of performing the initial configuration does not fire up.
  If it is the case for you please delete the repo you just created and start over again.

# Customization: 

## Changing the directory structures.

All your source files must remain inside the ``src`` dir, you can change how things are organized
but don't forget to update your ``package.json`` ``main``, ``type`` and ``files`` fields and ``tsconfig.esm.json`` ``include`` field when appropriate!

## Swipe the image in the ``README.md``

A good way to host your repo image is to open an issue named ASSET in your project, close it, create a comment, drag and drop the picture you want to use and that's it. You have a link that you can replace in the ``README.md``.  
While you are at it submit this image as *social preview* in your repos github page's settings so that when you share on
Twitter or Reddit you don't get your GitHub profile picture to show up.

## Disable CDN build 

If your project does not target the browser or if you are not interested in offering CDN distribution:

- Remove all ``cdn:*`` npm scripts and ``npm run cdn`` from the `build` script ( in ``package.json`` ).
- Remove ``./tsconfig.esm.json``
- Remove ``/dist/esm/`` entry from ``files`` in ``package.json`` 
- Remove ``simplifyify`` and ``terser`` from dev dependencies.

## Remove unwanted dev dependencies

Dev dependencies that are not required by the template ( you can safely remove them if you don't use them ):

- ``evt``
- ``@types/node``

Must keep:

- ``typescript``
- ``denoify`` ( for the script that moves dist files to the root before publishing )
- ``simplifyify`` ( for CDN build )
- ``terser`` ( for CDN build )

## Customizing the Badges

You can use [shields.io](https://shields.io) to create badges on metrics you would like to showcase.

# Accessing files outside the ``dist/`` directory

The drawback of having short import path is that the dir structure  
is not exactly the same in production ( in the npm bundle ) and in development.

The files and directories in ``dist/`` will be moved to the root of the project.  

As a result this won't work in production: 

``src/index.ts``
```typescript
import * as fs from "fs";
import * as path from "path";

const str = fs.readFileSync(
    path.join(__dirname,"..", "package.json")
).toString("utf8");
```

Because ``/dist/index.js`` will be moved to ``/index.js``

You'll have to do: 

``src/index.ts``
```typescript
import * as fs from "fs";
import * as path from "path";
import { getProjectRoot } from "./tools/getProjectRoot";

const str = fs.readFileSync(
    path.join(getProjectRoot(),"package.json")
).toString("utf8");
```

# Video demo

[![Watch the video](https://user-images.githubusercontent.com/6702424/82117367-c32ea700-976f-11ea-93f9-ec056aebc528.png)](https://youtu.be/Q5t-yP2PvPA)

# Examples of auto-generated readme:

![npmjs com](https://user-images.githubusercontent.com/6702424/82402717-70017080-9a5d-11ea-8137-0bfa9a139655.jpg)

# Creating a documentation website for your project:

I recommend [GitBook](https://www.gitbook.com), It enables you to write your documentation in markdown from their 
website and get the markdown files synchronized with your repo.
They will provide you with a nice website for which you can customize the domain name.  
All this is covered by their free tier.  

Example: 
- [repo](https://github.com/garronej/evt)
- [GitBook documentation website](https://docs.evt.land)

I advise you to have a special directory at the root of your project where the markdown documentation files
are stored. It is configured by placing a ``.gitbook.yaml`` file at the root of the repo containing, for example:
``root: ./docs/``

PS: I am not affiliated with GitBook in any way.

# Creating a landing page for your project.

Beside the documentation website, you might want to have a catchy landing page to share on social networks.  
You can use [GitHub pages](https://pages.github.com) to host it. 

If you like the landing page of EVT, [evt.land](http://evt.land), you can fork the [repo](https://github.com/garronej/evt.land) and adapt it for your module.  

You'll just have to go to settings and enable Pages.

![image](https://user-images.githubusercontent.com/6702424/82155402-0aeb2680-9875-11ea-9159-f6167ee2928e.png)

And update your DNS: 

![image](https://user-images.githubusercontent.com/6702424/82155473-7e8d3380-9875-11ea-9bba-115cbb3ef162.png)

I personally use [Hurricane Electric](https://dns.he.net) free DNS servers because they support a lot of record types.
However, if your DNS provider does not support ``ALIAS``, you can use ``A`` records and manually enter the IP of GitHub servers.
I let you consult the [GitHub Pages Documentation](https://help.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain). 
