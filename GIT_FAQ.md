# GIT Шпаргалка

## Configure Git for the first time
```
git config --global user.name "Name Surname"
git config --global user.email "NameSurname@email.com"
```

## Working with your repository

### I just want to clone this repository
If you want to simply clone this empty repository then run this command in your terminal.
```
git clone ssh://git@domain.com:7998/project/repo.git
```

### My code is ready to be pushed
If you already have code ready to be pushed to this repository then run this in your terminal.
```
cd existing-project
git init
git add --all
git commit -m "Initial Commit"
git remote add origin ssh://git@domain.com:7998/project/repo.git
git push -u origin HEAD:master
```

### My code is already tracked by Git
If your code is already tracked by Git then set this repository as your "origin" to push to.
cd existing-project
```
git remote set-url origin ssh://git@domain.com:7998/project/repo.git
git push -u origin --all
git push origin --tags
```
