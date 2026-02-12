# what is gh
it's a GitHub in CLI to manage all repositories in github using your terminal only 

# how to edit my repo
1. show all Github Repositories `gh repo ls` 
2. write this command to enter your repo `gh repo clone <owner\repo>`
3. apply your edits
4. commit ``
5. push ``

- show all Github Repositories
`gh repo ls`

- Show README file of a repo: 
`gh repo view <owner/repo>`

- Open a repository in your web browser: 
`gh repo view <owner/repo> --web`

- Clone (download) a repository to your PC: 
`gh repo clone <owner/repo>`

- Edit repository description: 
`gh repo edit --description "Your new description"`

- Change repository visibility (Private/Public): 
`gh repo edit --visibility <private|public>`

- Set your favorite text editor for the CLI: 
`gh config set editor <editor_name>`

- Check your current login status: 
`gh auth status`

# how to add folder and push NEW files

