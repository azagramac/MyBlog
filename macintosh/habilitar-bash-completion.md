# Habilitar Bash Completion

Lo primero sera establecer por defecto bash en lugar de zsh

```shell
chsh -s /bin/bash
```

\
Instalamos el gestor de paquetes brew

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

La instalacion durara unos minutos, despues reiniciar el Mac.\
\
\
Instalar bash completion

```shell
brew install bash-completion
```

Y para terminar, metemos una linea en el .bash\_profile de nuestro usuario

```shell
echo "[ -f /usr/local/etc/bash_completion ] && . /usr/local/etc/bash_completion" >> ~/.bash_profile
```

```shell
source ~/.bash_profile
```

\
Testing

```shell
$ git [tab][tab]
add               commit            gc                mv                request-pull      stage 
am                config            gitk              notes             reset             stash 
apply             checkout          grep              prune             restore           status 
archive           cherry            gui               pull              revert            submodule 
bisect            cherry-pick       help              push              rm                switch 
blame             describe          init              range-diff        scalar            tag 
branch            diff              instaweb          rebase            send-email        whatchanged 
bundle            difftool          log               reflog            shortlog          worktree 
citool            fetch             maintenance       remote            show              
clean             format-patch      merge             repack            show-branch       
clone             fsck              mergetool         replace           sparse-checkout
```
