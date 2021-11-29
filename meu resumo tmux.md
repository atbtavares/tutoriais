C-b   =    Ctrl + b
M-b   =    Alt  + b
S-    =    Shift
C-M-x =    Ctrl + Alt + x



BUFFER MODE
TREE MODE
CLIENT MODE


tmux new

tmux new -smysession

tmux new 'vim /etc/network/interfaces'


tmux new -nmytopwindow top


C-b ? 

C-b / <tecla>

tmux new-session -d -nmysession


C-b : 

tmux attach -tmysession

tmux attach -dtmysession

tmux new -Asmyssession

tmux ls

:kill-server



C-b c 

-d cria -n dá nome
:neww -dnmynewwindow

-t especifica alvo para janela
:neww -t999

:neww top


SPLITTING THE WINDOW

C-b %

C-b "

:split-window { -h  -v  }

:split-window

tmux splitw -v
tmux splitw -h


tmux splitw -f

poe nova pane left or above the pane being split instead right below
tmux splitw -b

cria mas não deixa ativa
tmux splitw -d


CHANGING THE CURRENT WINDOW

C-b 0    0, 2, ..., 9

C-b '  prompt para mudar a janela

C-b n
C-b p

C-b l vai para a ultima janela (que é a janela que era a 'janela atual' antes da atual 'janela atual')



CHANGING THE ACTIVE PANE

C-b up
C-b down
C-b left
C-b right

C-b q prints pane numbers and sizes
C-b C-o troca o quadro com o quadro ativo, trocando posição e tamanho dentro da janela


CHOOSING SESSIONS, WINDOWS AND PANES

C-b s   show only sessions with attached sessions
C-b w   show 



DETACHING OTHER CLIENTS






KILLING A SESSION, WINDOW OR PANE

C-b & fecha a janela atual pedindo confirmação / kill-windows

C-b x fecha apenas a janela ativa / kill-pane



RENAMING SESSIONS AND WINDOWS 

C-b $   / rename-session
C-b     / rename-window


SWAPPING AND MOVING

/ swap-pane swap-window

C-b m 
C-b M

:swap-pane
C-b {  and  C-b }

:move-window
C-b .

:move-window -kt999
:movew -r   remove gaps entre os números


RESIZING AND ZOOMING PANES

:resize-pane
C-b C-left     [right, up, down]
C-b M-Left
C-b Z




WINDOW LAYOUTS

C-b Space
C-b M-1     C-b M-2   ...


COPY AND PASTE - buffer mode: buffer0 buffer1 ... 50
C-b [  copy mode
C-b ]  paste 

Up, Down, Left, Right 	Move the cursor
C-Space Start a selection
C-w 	Copy the selection and exit copy mode
q    	Exit copy mode
C-g 	Stop selecting without copying, or stop searching
C-a 	Move the cursor to the start of the line
C-e 	Move the cursor to the end of the line
C-r 	Search interactively backwards
M-f 	Move the cursor to the next word
M-b 	Move the cursor to the previous word



C-b =   Abre os buffers pra vc escolher
Enter 	Paste selected buffer
p 	Paste selected buffer, same as Enter
P 	Paste tagged buffers
d 	Delete selected buffer
D 	Delete tagged buffers


:setb -bbuffer0 -nmybuffer    renomear buffer
:setb -bfoo bar               criar novo buffer com nome
:loadb -bbuffername ~/a/file  carregar de um arquivo

set-buffer or load-buffer without -b creates an automatic buffer.
:saveb -bbuffer0 ~/saved_buffer salva buffer para um arquivo



FINDING WINDOWS AND PANES

C-b f pede um texto e mostra somente as ocorrencias dele nos quadros com o tree mode


USING THE MOUSE

:set -g mouse on

Once the mouse is enabled:

    Pressing the left button on a pane will make that pane the active pane.
    
    Pressing the left button on a window name on the status line will make that the current window.
    
    Dragging with the left button on a pane border resizes the pane.
    
    Dragging with the left button inside a pane selects text; the selected text is copied when the mouse is released.
    
    Pressing the right button on a pane opens a menu with various commands. When the mouse button is released, the selected command is run with the pane as target. Each menu item also has a key shortcut shown in brackets.
    
    Pressing the right button on a window or on the session name on the status line opens a similar menu for the window or session.


CONFIGURING TMUX


THE CONFIGURATION FILE

:source ~/.tmux.conf


KEY BINDINDS

key tables:  
root         / key bindings for keys pressed without the prefix key
prefix       / key bindings for keys pressed after the prefix key
copy-mode    / emacs
copy-mode-vi


 - - - - exemplos de uso - - - - -

$ tmux lsk -Tprefix
bind-key    -T prefix C-b     send-prefix
bind-key    -T prefix C-o     rotate-window
...


$ tmux lsk -Tprefix -N
C-b     Send the prefix key
C-o     Rotate through the panes
...

bind-key commands can be used to set a key binding, either interactively or most commonly from the configuration file. Like list-keys, bind-key has a -T flag for the key table to use. If -T is not given, the key is put in the prefix table; the -n flag is a shorthand for -Troot to use the root table.

For example, the list-keys command shows that C-b 9 changes to window 9 using the select-window command:

$ tmux lsk -Tprefix 9
bind-key -T prefix 9 select-window -t :=9


A similar key binding to make C-b M-0 change to window 10 can be added like this:
bind M-0 selectw -t:=10






Copy mode key bindings





wendel pinheiro







10.10.3.158



set -g mouse on
setw synchronize-panes on


C-b [  copy mode
C-b ]  paste 
C-Space Start a selection
C-w 	Copy the selection and exit copy mode

C-b [  copy mode
C-b ]  paste 