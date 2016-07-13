# maker
This is really easy script for easy running make with parameters.

## Install
Best way right now is to clone this repository somewhere and link maker into ~/bin or add it to your $PATH

## Usage
You have to copy one of mine Makefiles or create your own. After that you can run maker $TARGET $PARAMS and all $PARAMS will be pased to make as variable (look in some Makefile)

## Example
 - Create project in some folder
 - Copy my docker-compose.yaml and Makefile into (or create your own)
 - Install maker
 - run `maker help` for show help - only for my Makefile
 - run any command with parameters example `maker composer update`

## Plans
 - automatic checking of new version of maker
 - automatic install of choisen Makefile
 - automatic update of installed Makefile
