---
layout: post
title:  "Emacs Guide for Mac OSX"
categories: Emacs
tags: [emacs]
---

A detailed note about Emacs installation, configuration in OSX, and its guide.

We all know that Emacs is a very powerful text editor that is flexible, customizable, and extensible. More importantly it can utilize different packages from MELPA to help with text editting. I have noticed that there isn't a detail-oriented note or blog about how to install Emacs through homebrew, configure emacs in OSX, and use it. Therefore, I am going to write a blog for it.

## Emacs Installation
In Mac OSX, there is a default emacs text editor that cannot be updated regularly because it directly comes along as a package from the Mac OSX itself. You have to update the whole operating system in order to get it updated, which, most of the time, is an out-dated version. Therefore, if you want to update emacs regularly, you may want to use the emacs provided by homebrew.

If you want to install homebrew, you can just paste the following command into your terminal and run it:

{% highlight css %}
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}

If you have homebrew installed already, you can run the following commands to install homebrew's version of emacs:

{% highlight css %}
$ brew update
$ brew install emacs --with-cocoa
$ brew linkapps emacs
{% endhighlight %}

The second step is optional, but it is recommended if you like to start Emacs from the launchpad or from Spotlight.

You can delete the default version of the emacs that comes along with the Mac OSX by running the following commands in the terminal
{% highlight css %}
$ sudo rm /usr/bin/emacs
$ sudo rm -rf /usr/share/emacs
{% endhighlight %}

And you can create an alias in your terminal shell and when you invoke "emacs" it will run the newly installed version.
{% highlight css %}
alias emacs="/usr/local/Cellar/emacs/26.x/Emacs.app/Contents/MacOS/Emacs -nw"
{% endhighlight %}

You can copy and paste it into a file ~/.bash_profile if you want the change to be permanent. Make sure you have the path to the emacs app correctly.You can search the path through using `cd`. For example, the directory to my emacs app is "/usr/local/Cellar/emacs/26.1_1/Emacs.app/Contents/MacOS/Emacs".

Once you have installed Emacs from homebrew correctly, you can just invoke emacs from the shell by `emacs`. You can also check the version of your emacs by `emacs --version`.

## Installing Packages in Emacs

Before using Emacs, I believe it is helpful if you can utilize the powerful MELPA, the largest and most up-to-date repository of Emacs packages, to enhance your experience with Emacs. In order to get the best out of MELPA, you have to create a file named `init.el` under this directory `~/.emacs.d/`. And then you copy and paste the following contents into this `init.el`:

{% highlight css %}
(require 'package)
(add-to-list 'package-archives
             '("melpa" . "http://melpa.org/packages/")
             t)
(package-initialize)
{% endhighlight %}

This will enable Emacs to know that it should install available packages from both MELPA and GNU, which is the default source of Emacs packages. You may want to restart Emacs in order for `init.el` to take effect.

Once you have it, you can start the Emacs and install the packages you want to use. When you want to install packages in Emacs, you should have the command `M-x package-install RET [package-name] RET` where the key `M` is the key ESC and you replace `[package-name]` with the package you want to install. For example, if I want to install flyCheck mode, I would run the command `M-x package-install RET flycheck RET` in Emacs to install flyCheck mode.

To begin your experience with Emacs, I would recommend some useful packages:
* `auto-complete`: it helps you automatically complete a word so that you can write codes much faster
* `flycheck`: it checks syntax of your codes
* `paredit`: a minor mode for editting parentheses (very useful if you write codes in Scheme/Racket)

Once you install a package, it may not be activated whenever you launch Emacs. You can manually activate it through using this command `M-x [package-name]` in an instance of Emacs. However, there is a way to activate the package whenever you use Emacs by adding the following lines to `init.el`:

{% highlight css %}
(require '[package-name])
(global-[package-name] t)

; in case of flyCheck mode, it will be
; (require 'flycheck)
; (global-flycheck-mode t)
{% endhighlight %}

Moreover, when you need to edit the codes of different languages, you should also install the language mode for them. For example, when I write codes in Racket, I also need to install racket-mode in order to have the right syntax highlight and spacing.

You can find out what packages you want to install with the command `M-x package-list-packages`, which gives the whole list of available Emacs packages from MELPA and GNU. You can also visit MELPA website to gain more information about different packages it has.

## Using Emacs

Emacs is not hard to use, but is not easy to use, either. It takse practices to remember the Emacs shortcut keys. If you use Emacs more and more frequently, eventually you can memorize all the shortcut keys. Here is a table of basic shortcut keys that you would use a lot in Emacs.

| Shortcut Keys | Description                                                                                                      |
| ------------- | ------------------------------------------------------------                                                     |
| C-x C-s       | Save the file                                                                                                    |
| C-x C-c       | Exit emacs (asks about unsaved buffer and running programs)                                                      |
| M-g g         | Go to a specific line given by line number                                                                       |
| C-x C-f       | Find file (or create if not there); prompts for file name                                                        |
| C-SPACE       | Mark one end of region                                                                                           |
| C-w           | Cut (after you Mark and move to other end)                                                                       |
| M-w           | Copy (after you Mark and move to other end)                                                                      |
| C-y           | Yank (paste) most recently killed (cut or copied); will also use text currently selected in another application. |
| ESC ESC ESC   | Get me out of where I am                                                                                         |
| C-v           | Scroll down (toward end of buffer)                                                                               |
| M-v           | Scroll up (toward beginning of buffer)                                                                           |
| C-M-v         | Scroll other window down                                                                                         |
| C-x 1         | One window on current buffer                                                                                     |
| C-x 2         | Split window vertically                                                                                          |
| C-x 3         | Split window horizontally                                                                                        |
| C-x 0         | Kill the splitted window                                                                                         |
| C-x u or C-_  | Undo/Redo                                                                                                        |
| C-g           | Quit (interrupt) command                                                                                         |

Noted: The key `C` stands for the key CONTROL in your keyboard.

There are more interesting shortcut keys you can use in Emacs, which you can google it for more information.

## References
* [homebrew](https://brew.sh/)
* [Installing Emacs on OSX](http://wikemacs.org/wiki/Installing_Emacs_on_OS_X)
* [MELPA](https://melpa.org/#/)
* [Emacs Cheat Sheet](http://www.rgrjr.com/emacs/emacs_cheat.html)
