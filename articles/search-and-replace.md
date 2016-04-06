### Search and replace string using Vim and some useful ruby gems

#### Why

Perform search and replace a time consuming task and is error prone.
I will try to show you one way to automate this process using the
power of my favourite editor Vim and some gems I have developed that is quite useful for this purpose.

#### What are we using

- [code_lister][]
- [extension_lister][]
- Recent version of Vim

#### How to do it

- First install [code_lister][] and [extension_lister][] gem

 * [code_lister][] provides similar function to the Unix `find` command implemented in Ruby.
 * [extension_lister][] will be used to list all of the extensions in the given directory so we know what to changes.

```sh
gem install code_lister

gem install extension_lister

# Refresh our ruby library (just in case)
rbenv rehash # if you are using rbenv, adjust appropriately if you are using RVM
```

- Using [extension_lister][] gem to list all of the extensions we have in the project

```sh
# Make a sample directory we are going to use

mkdir -p ~/codes/

# Clone the example project for use in this case my old github_copier

git clone git@github.com:agilecreativity/github_copier.git

# Change to this directory
cd ~/codes/github_copier

# Install the two gems above
gem install code_lister

gem install extension_lister
```

Now take a look at all of the extension we have in this project

```sh
➜  github_copier git:(master) extension_lister -b .
.gem
.gemspec
.lock
.md
.rb
```

From the result above, I know that I only want to use the following files in the search-and-replace.

`.gemspec`, `.md`, and `.rb`

I will skip the `.gem` and `.lock` as they are generated files by the `bundle` command.

For the file without the extensions I like to use `LICENSE,Rakefile,Guardfile,github_copier`

```sh
➜  github_copier git:(master) code_lister -b . -e rb,md,gemspec -f LICENSE,Rakefile,Guardfile,github_copier
./CHANGELOG.md
./Guardfile
./LICENSE
./README.md
./Rakefile
./bin/github_copier
./github_copier.gemspec
./lib/github_copier
./lib/github_copier.rb
./lib/github_copier/filename_cleaner.rb
./lib/github_copier/github_copier.rb
./lib/github_copier/opt_parser.rb
./lib/github_copier/version.rb
./test/lib/github_copier
./test/lib/github_copier/test_github_copier.rb
./test/test_helper.rb
➜  github_copier git:(master)
```

As you can see from the result above, we get all the input file that we needed to be used with Vim in the next step.

- Prepare the Vim script to automate the search and replace

Create the following file called `search-and-replace.vim` (or any other name you like)

That contains the following detail:

```vim
:silent! argdo %s/github_copier/github_cloner/ge | update | %s#GithubCopier#GithubCloner#ge | update | wq
```

- Now we are ready to see the magic

We need use the result of the files and the two string we like to search and replace in these files

```sh
vim `code_lister -b . -e rb,md,gemspec -f LICENSE,Rakefile,Guardfile,github_copier -r` -s ./search-and-replace.vim
```

And magic, we have run the search and replace all off of the occurence of the files in the given project using Vim.

[code_lister]: https://rubygems.org/gems/code_lister
[extension_lister]: https://rubygems.org/gems/extension_lister
