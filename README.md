# PD Lecture Notes

## To Get The latest updates for lecture notes

1. Install Git on local machine (If you don't have `Git` installed)
	- Download [Git](https://git-scm.com/downloads)
	- First time setup :

```bash
git config --global user.name "Your Name"
git config --global user.email "email@email.com"
```

2. Clone the repository to local machine

```bash
git clone https://github.com/Muhammad-Ashraf9/PD.git
```

3. Pull the latest changes every time before viewing the files

```bash
git pull
```

### Files are in .md format (Obsidian)

4.  Best way to view the files is to use [obsidian](https://obsidian.md/)

5.  Open the folder in obsidian as vault
![Open folder as vault](assets/open-folder-as-vault.png)
6.  Open the file you want to view in obsidian

- you can search in all files
- use `ctrl+p` to search for commands

### To Get latest updates for lectures notes Automatically

7.  Install  `Git`  plugin From Community plugins

- settings => community plugins => turn off Restricted mode  
![](assets/steps-community-plugins.png)

- Browse  community plugins

![community plugins](assets/community-plugins.png)
- search for  `Git`  => install  `Git` plugin -> enable plugin

![](assets/search-git.png)
- set `Git` plugin `options` 

![](assets/obsidian-git-options.png)
- auto pull X  ( up to you )  minutes

![auto-pull-interval](assets/auto-pull-interval.png)

---

## To Contribute

1. **Fork the repository** 
	- Navigate to the repository on GitHub 
	- Click on the `Fork` button in the top-right corner to create a copy in your GitHub account 
1. **Clone your forked repository** 
	- Copy the URL of your forked repository 
	- Open your terminal and navigate to the desired directory 
	- Run the following command: 
	
	```bash  
	git clone <url-of-your-forked-repository>   
	``` 
	

1.  **Create a new branch**
	
	```bash
	   git checkout -b <branch-name>
	```

1. **Make changes to the files** 
	- use similar [Callouts Formatting](Callouts%20Formatting.md)
1. **Add the changes to staging area**
	
	```bash
	   git add .
	```

1. **Commit the changes**
	
	```bash
	   git commit -m "commit message"
	```

1. **Push the changes to remote repository**
	
	```bash
	   git push origin <branch-name>
	```

1. **Create a pull request from `Github` website**
	- Navigate to the **original** repository on GitHub
	- You should see a prompt to create a **pull request** from your forked repository
	- Click on the **"Create Pull Request"** button and follow the instructions to create a **PR**
	- Your **PR** will be reviewed
	- Once approved, your **PR** will be merged into the main branch

---
### Contributors

<a href="https://github.com/Muhammad-Ashraf9/PD/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Muhammad-Ashraf9/PD" />
</a>

