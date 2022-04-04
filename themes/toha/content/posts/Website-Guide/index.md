---
title: "How to build your own Hugo Website and host it for free on Github Pages"
date: 2021-09-12
description: "I walk through the steps required to get a website up and running fast and easy using Hugo and Github Pages"
menu:
  sidebar:
    name: Build your own Hugo website
    identifier: Build your own Hugo website
    weight: 1
---

### Create a Repository

Firstly you'll want to create a repository for this on Github to make changes easier to update with git commit and push rather than uploading files manually every time you make changes. As this will be a Github Page you will want to name the repo as '<your Github name>.github.io'. Yes the .io here is important so make sure you add that.

Clone this repository to your local machine and cd to it, I added mine under the users documents folder.

### Create the Hugo Site

You'll want to get the basic Hugo site next by using the command:

```
hugo new site ./ -f=yaml --force
```

The `-f=yaml` makes your YAML configuration file while the `--force` command is optional and only forces Hugo to build the site if theres files already in the directory so its unnecessary if you just created the directory.

### Pick a Theme

This part is the most important and should take you the longest. Before you go having a look around the themes first take the time to think about what you are making this site for and maybe note down what the theme needs to have and what things you'd like it to have but aren't as necessary.

Then head over to https://themes.gohugo.io/ and have a look at the tags on the right side. Chances are you already have in mind the kind of site you want. I was after a Portfolio site so that tag was the obvious choice for me to begin at.

When you've found the theme you want you will have to click download and this will bring you to the github page for that theme. From there you will need to use the command `git submodule add https://the-github-link.git themes/theme-name`. I chose Toha for my theme so my command looks like:

```
git submodule add https://github.com/hugo-toha/toha.git themes/toha
```

### Run the Site

You will want to test that its all working now and be able to see what changes you make appear like on the site locally before you commit those changes publicly on Github. To do this you will need to run the following command:

```
hugo server -t toha -w
```

Note that you will have to change `toha` to whatever theme you chose if you chose a different one. Running this opens up port 1313 by default to host your site locally and can be reached by going to http://localhost:1313.

### Configure the Site

Before you get started on the personal stuff it's time to configure the basics first. Remember that yaml file we had Hugo create at the start? It is `config.yaml` and it is located in the root directory of your repository. Open it up in your favorite text editor, I personally use Atom for this. You'll want to replace the default content with this:

```
#baseURL: https://hugo-toha.github.io

languageCode: en-us
title: "Crystal Mercier"
theme: "toha"

# Manage languages
# For any more details, you can check the official documentation: https://gohugo.io/content-management/multilingual/
languages:
  en:
    languageName: English
    weight: 1

# Force a locale to be use, really useful to develop the application ! Should be commented in production, the "weight" should rocks.
# DefaultContentLanguage: bn

# Allow raw html in markdown file
markup:
  goldmark:
    renderer:
      unsafe: true
  tableOfContents:
    startLevel: 2
    endLevel: 6
    ordered: false

# At least HTML and JSON are required for the main HTML content and
# client-side JavaScript search
outputs:
  home:
  - HTML
  - RSS
  - JSON

# Enable Google Analytics
googleAnalytics: UA-122321624-2

# Enable global emoji support
enableEmoji: true

# Site parameters
params:
  # Background image of the landing page
  background: /images/default-background.jpg

  # Provide logos for your site. The inverted logo will be used in the initial
  # transparent navbar and the main logo will be used in the non-transparent navbar.
  logo:
   main: /images/site/main-logo.png
   inverted: /images/site/inverted-logo.png

  # GitHub repo of your site
  #gitRepo: https://github.com/hugo-toha/hugo-toha.github.io
  # Default branch of your Git repo
  #gitBranch: source

  # Configure various features of this theme
  features:
    # Enable and configure portfolio
    portfolio:
      enable: true
    # Enable and configure blog posts
    blog:
      enable: true
    # Enable & configure "Notes" features
    notes:
      enable: true


  # specify whether you want to show Table of Contents in reading page
  enableTOC: true

  # If you want to use different country flag for a language, specify them here.
  # flagOverwrites:
  # - languageCode: en
  #   countryCode: us

  # Provide footer configuration.
  footer:
    enable: true
    # You can provide your custom footer template using this option.
    # Put your template in "layouts/partials" folder of your repo.
    template: footer.html
    # Show/hide navigation in the footer. Default is "true".
    navigation:
      enable: true
    # Show/hide "Contact Me" section in the footer. Default is "true".
    contactMe:
      enable: true
    # Show/hide credentials section in the footer. Default is "true".
    credentials:
      enable: true

    # Show/hide newsletter section in the footer. Default is "true".
    # Currently, it supports "mailchimp".
    newsletter:
      enable: false
      provider: mailchimp
      mailchimpURL: https://github.us1.list-manage.com/subscribe/post?u=19de52a4603135aae97163fd8&amp;id=094a24c76e

    # Show/hide disclaimer notice in the footer. Default is "false".
    disclaimer:
      enable: false
  ```

If you used a different theme I suggest Googling configuration examples or complete documentation for that theme instead of just copying this. If you are using Toha theme make sure you change the `title:` and more configurable options can be found at https://toha-guides.netlify.app/posts/configuration/site-parameters/.

### Add Data

There should be a `data` folder in the root directory and inside this folder should be another called `en` for English. If one or both of these are missing then you'll need to create them.

##### Site
First you'll add site information by creating a file named `site.yaml` within the `/data/en/` directory and add the following content:

```
# Copyright Notice
copyright: © 2020 Copyright.

# Meta description for your site.  This will help the search engines to find your site.
description: Portfolio and personal blog of Crystal Mercier.
```

Change the description to whatever you want yours to be.

##### Author

Next create an `author.yaml` file within the `/data/en/` directory and add the following in it:

```
# some information about you
name: "Crystal Mercier"
nickname: "Crystal"
# greeting message before your name. it will default to "Hi! I am" if not provided
greeting: "Hi, My name is"
image: "images/author/crystal.png"
# give your some contact information. they will be used in the footer
contactInfo:
  email: "cmmercier86@gmail.com"
#  phone: "+0123456789"

# some summary about what you do
summary:
  - I am a Bug Bounty Hunter
  - I am a Security Researcher
  ```

  Again take some time to go over this and change the information to your own before saving.

#### Sections

  Now that the basics are set you will need to add a new directory named `sections` in `/data/en` and move into this new directory to add some more files.

##### About

  Create a file named `about.yaml` and copy the following inside it:

  ```
  # section information
  section:
    name: About
    id: about
    enable: true
    weight: 1
    showOnNavbar: true
    template: sections/about.html

  # your designation
  designation: Security Researcher
  # your company information
  #company:
  #  name: Example Co.
  #  url: "https://www.example.com"

  # your resume. this file path should be relative to you "static" directory
  resume: "files/resume.pdf"

  # a summary about you
  summary: 'I am a passionate about technology and fitness. I enjoy constantly learning new things and helping out others. This blog is to share helpful bits of information about various topics in IT and also topics that happen to interest me at the time. Hopefully as a visitor to the site you’ll find the blog posts here useful.'
  # your social links
  # give as many as you want. use font-awesome for the icons.
  socialLinks:
  - name: Email
    icon: "fas fa-envelope"
    url: "cmmercier86@gmail.com"
  - name: Github
    icon: "fab fa-github"
    url: "https://www.github.com/Cyb3rWulfengrav"
  - name: LinkedIn
    icon: "fab fa-linkedin"
    url: "https://www.linkedin.com/in/crystalmercier/"
  - name: Twitter
    icon: "fab fa-twitter"
    url: "https://twitter.com/CM3rc13r"

  # your soft skills
  # give the percentage between 50 to 100 with 5 intervals.
  # currently supported colors: blue, yellow, pink, green, sky, orange
  badges:
  - type: certification
    name: CompTIA Security+
    url: "https://www.credly.com/org/comptia/badge/comptia-security-ce-certification"
    badge: "https://images.credly.com/size/340x340/images/74790a75-8451-400a-8536-92d792c5184a/CompTIA_Security_2Bce.png"

  - type: soft-skill-indicator
    name: Critical Thinking
    percentage: 100
    color: yellow

  - type: soft-skill-indicator
    name: Communication
    percentage: 100
    color: pink

  - type: soft-skill-indicator
    name: Problem Solving
    percentage: 100
    color: orange

  - type: soft-skill-indicator
    name: Adaptability
    percentage: 100
    color: green

  - type: soft-skill-indicator
    name: Team Work
    percentage: 100
    color: sky
```

Again change the information before saving and exiting this file. Also the `resume.pdf` should be supplied in the `/static/files` directory of your repository.

##### Skills

Next create the `skills.yaml` and copy the following:

```
# section information
section:
  name: Skills
  id: skills
  enable: true
  weight: 2
  showOnNavbar: true
  # Can optionally hide the title in sections
  # hideTitle: true
# Your Skills.
# Give a summary of you each skill in the summary section.
skills:
- name: Linux
  logo: "/images/sections/skills/linux.png"
  summary: "Kali and Ubuntu in Virtual Environments, Basic Bash Commands, File System Hierarchy, User Account Management, Network Firewalls."

- name: Windows
  logo: "/images/sections/skills/windows.png"
  summary: "Hardware, Active Directory, Powershell, Supporting and Troubleshooting, Backup and Restore, Security and Networking."

- name: Risk Analysis
  logo: "/images/sections/skills/riskanalysis.png"
  summary: "Identifying network vulnerabilities and risks. Configuring Networks in a secure manner to protect against breaches, malware and insider threats."

- name: Security Assessment Tools
  logo: "/images/sections/skills/tools.png"
  summary: "Kali Linux, Nmap, Burp Suite, OWASP ZAP Proxy, SQLMap, Wireshark, Nessus, John the Ripper, Hydra..etc"

- name: Web Technologies
  logo: "/images/sections/skills/web.png"
  summary: "HTTP protocol, HTTP status codes and headers, the basics of network security, basic understanding of web programming languages."

- name: Web Hacking
  logo: "/images/sections/skills/webcode.png"
  summary: "Knowledgeable in web application security vulnerabilities like XSS, CSRF, and RCE."
```

Change what you need to and save before closing.

##### Experiences

Next create `experiences.yaml` and add the following:

```
# section information
section:
  name: Experiences
  id: experiences
  enable: false
  weight: 3
  showOnNavbar: true
  # Can optionally hide the title in sections
  # hideTitle: true

# Your experiences
experiences:
- company:
    name: Example Co.
    url: "https://www.example.com"
    location: Dhaka Branch
    # company overview
    overview: Example Co. is a widely recognized company for cloud-native development. It builds tools for Kubernetes.
  positions:
  - designation: Senior Software Engineer
    start: Nov 2019
    # don't provide end date if you are currently working there. It will be replaced by "Present"
    # end: Dec 2020
    # give some points about what was your responsibilities at the company.
    responsibilities:
    - Design and develop XYZ tool for ABC task
    - Design, develop and manage disaster recovery tool [Xtool](https://www.example.com) that backup Kubernetes volumes, databases, and cluster's resource definition.
    - Lead backend team.

- company:
    name: PreExample Co.
    url: "https://www.preexample.com"
    location: Nowhere
    overview: PreExample Co. is a gateway company to enter into Example co. So, nothing special here.
  positions:
  - designation: Software Engineer
    start: March 2016
    end: May 2017
    responsibilities:
    - Write lots of example codes.
    - Read lots of examples.
    - See lots of example videos.
```
Again Change what you need to and save before closing.

##### Projects

Create `projects.yaml` and add the following to it:

```
# section information
section:
  name: Projects
  id: projects
  enable: true
  weight: 6
  showOnNavbar: true
  # Can optionally hide the title in sections
  # hideTitle: true

# filter buttons
#buttons:
#- name: All
#  filter: "all"
#- name: Professional
#  filter: "professional"
#- name: Academic
#  filter: "academic"
#- name: Hobby
#  filter: "hobby"

# your projects
projects:
- name: TryHackMe Write-Ups
  logo: /images/sections/projects/tryhackme.png
  role: Owner
  timeline: "July 2021 - Present"
  repo: https://github.com/Cyb3rWulfengrav/THM_Write-Ups
  #url: ""  # If your project is not a public repo but it has a website or any external details url then provide it here. don't provide "repo" and "url" simultaneously.
  summary: Write-ups for various hacktivities on tryhackme.com.
  tags: ["Write-up", "TryHackMe", "Hacktivities"]

- name: HackTheBox
  logo: /images/sections/projects/hackthebox.png
  role: Owner
  timeline: "July 2021 - Present"
  repo: https://github.com/Cyb3rWulfengrav/HTB_Write-Ups
  #url: ""
  summary: My collection of write-ups for the site hackthebox.eu.
  tags: ["Write-up", "HackTheBox","Hacktivities"]

#- name: Toha
#  logo: /images/sections/projects/toha.png
#  role: Owner
#  timeline: "Jun 2019 - Present"
#  repo: https://github.com/hossainemruz/toha
#  summary: A Hugo theme for personal portfolio.
#  tags: ["hobby","hugo","theme","professional"]
```

Change what you need to and save before closing.

##### Recent Posts

Create the `recent-posts.yaml` file and add the following:

```
# section information
section:
  name: Recent Posts
  id: recent-posts
  enable: true
  weight: 7
  showOnNavbar: true
```

The only things you might want to change here are the weight (decides what order it appears in relation to the other sections) and whether you want it on the Navbar or not.

##### Achievements

Create `achievements.yaml` file and add the following:

```
# section information
section:
  name: Achievements
  id: achievements
  enable: false
  weight: 6
  showOnNavbar: true
  # Can optionally hide the title in sections
  # hideTitle: true

# Your achievements achievements
achievements:
- title: Best Presenter
  image: /images/sections/achievements/presenter.jpg
  summary: Best presenter in the 2020 XYZ conference.
- title: Champion
  image: /images/sections/achievements/sport.jpg
  summary: Champion in cycling inter-city cycling championship 2020.
- title: Graduation
  image: /images/sections/achievements/graduation-cap.jpg
  summary: Received Bachelor of Science (B.Sc.) in Computer Science and Engineer from XYZ University.
- title: Award Winner
  image: /images/sections/achievements/woman-winner.jpg
  summary: Wined best paper award at IEE Conference 2020.
```

Set enable to true and change the information if you wish to use this section.

### Deploy site

In the root directory run the command `hugo`, this builds the site and adds the contents to the folder `public`. Move into this folder and use the following commands:

```
git init
git add .
git remote add origin https://github.com/username/username.github.io.git
git commit -m "first commit"
git push origin master
```

The `git push` step will ask for your username and password. Note that Github passwords do not seem to work for this anymore and the password is really asking for your Github personal access token. If you do not have a Github personal access token set you must do that now. When this successfully completes your new site will be up and running at `https://<your username>.github.io.`

##### Personal Access Token

Open Github and go to `settings` on the menu that appears when you click your icon. From there click on `Developer settings` and the `Personal access token` section. Click `Generate new token` and give it a descriptive name and expiration date then give it the permissions you require and click `Generate token`.

Use this token as the password and your push request will complete.

### Add Posts

To start adding new posts to your site you have to go to `themes/toha/content` and `posts`, if `posts` directory does not exist already you must create it then `cd posts`. Now create a file named `_index.md` within `themes/toha/content/posts` and add the following:

```
---
title: Posts
---
```

Next create a directory for your first post and `cd` into this new directory. Now create a file named `index.md`, this is a Markdown file and follows the Markdown syntax. Here is a quick cheat sheet for reference https://www.markdownguide.org/cheat-sheet/. Enter the following to the `index.md` file to get started:

```
---
title: "How to build your own Hugo Website and host it for free on Github Pages"
date: 2021-09-12
description: "I walk through the steps required to get a website up and running fast and easy using Hugo and Github Pages"
menu:
  sidebar:
    name: Build your own Hugo website
    identifier: Build your own Hugo website
    weight: 1
---
```

Change the information to match your own post and begin writing your post below those `---` lines. Happy posting!

### Notes

To configure sections: `data/en/`

Image file location: `themes/toha/static/images`

Theme images location: `themes/toha/assets/images`

Posts location: `themes/toha/content/posts`
