Custom git-prompt:
https://gist.github.com/adojos/fd30baa716dbfbe282fe147ab717b664
Informative git-prompt:
https://github.com/magicmonty/bash-git-prompt

##################################################################################

# Change in:
C:\Program Files\Git\etc\profile.d\git-prompt.sh

# Full git-prompt (where git_ps1 is defined) in:
C:\Program Files\Git\mingw64\share\git\completion/git-prompt.sh

# Vars for colors
prompt_color='\[\033[;32m\]'	# Color green
info_color='\[\033[1;34m\]'		# Color blue
prompt_symbol=😃
if [ "$EUID" -eq 0 ]; then # Change prompt colors for root user
	prompt_color='\[\033[;94m\]'	# Color light blue
	info_color='\[\033[1;31m\]'		# Color red
	# Skull emoji for root terminal
	prompt_symbol=💀
fi

if test -f /etc/profile.d/git-sdk.sh
then
	TITLEPREFIX=SDK-${MSYSTEM#MINGW}
else
	TITLEPREFIX=$MSYSTEM
fi

if test -f ~/.config/git/git-prompt.sh
then
	. ~/.config/git/git-prompt.sh
else
	PS1='\[\033]0;$TITLEPREFIX:$PWD\007\]' # set window title
	PS1="$PS1"'\n'					# new line
	PS1="$PS1"$prompt_color			# prompt color green
	PS1="$PS1"'┌──('
	PS1="$PS1"$info_color			# info color blue
	PS1="$PS1"'\u'
	PS1="$PS1"$prompt_symbol
	PS1="$PS1"'\h'					# the hostname up to the first ‘.’
	#PS1="$PS1"'\[\033[35m\]'		# change to purple
	#PS1="$PS1"'$MSYSTEM '			# show MSYSTEM
	PS1="$PS1"$prompt_color			# prompt color green
	PS1="$PS1"')-['
	PS1="$PS1"'\[\033[0;1m\]'		# reset color
	PS1="$PS1"'\w'					# current working directory
	PS1="$PS1"$prompt_color			# prompt color green
	PS1="$PS1"']'
	PS1="$PS1"'\[\033[0;1m\]'		# reset color
	if test -z "$WINELOADERNOEXEC"
	then
		GIT_EXEC_PATH="$(git --exec-path 2>/dev/null)"
		COMPLETION_PATH="${GIT_EXEC_PATH%/libexec/git-core}"
		COMPLETION_PATH="${COMPLETION_PATH%/lib/git-core}"
		COMPLETION_PATH="$COMPLETION_PATH/share/git/completion"
		if test -f "$COMPLETION_PATH/git-prompt.sh"
		then
			. "$COMPLETION_PATH/git-completion.bash"
			. "$COMPLETION_PATH/git-prompt.sh"
			PS1="$PS1"'\[\033[95m\]'  # change color to light magenta
			PS1="$PS1"'`__git_ps1`'   # bash function
		fi
	fi
	PS1="$PS1"'\[\033[0m\]'		# change color
	PS1="$PS1"'\n'				# new line
	PS1="$PS1"$prompt_color	   	# prompt color green
	PS1="$PS1"'└─'
	PS1="$PS1"$info_color	   	# info color blue
	PS1="$PS1"'$ '				# prompt: always $
	PS1="$PS1"'\[\033[0m\]'		# Reset prompt color for typing
fi

MSYS2_PS1="$PS1"               # for detection by MSYS2 SDK's bash.basrc

# Evaluate all user-specific Bash completion scripts (if any)
if test -z "$WINELOADERNOEXEC"
then
	for c in "$HOME"/bash_completion.d/*.bash
	do
		# Handle absence of any scripts (or the folder) gracefully
		test ! -f "$c" ||
		. "$c"
	done
fi



##################################################################################