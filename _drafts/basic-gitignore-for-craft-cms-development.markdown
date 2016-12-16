--- 
title: Basic .gitignore for Craft CMS development 
date: 2015-01-04 11:53:00 
layout: post 
url: http://supergeekery.com/geekblog/comments/basic-.gitignore-for-craft-cms-development 
---
I wanted to share my basic **.gitignore** file for when I'm working on a Craft CMS project. Sometimes I use CodeKit, sometimes I don't but this **.gitignore** file will take care of CodeKit cache just in case.

	# Mac specific
	.DS_Store
	.DS_Store?
	.AppleDouble
	.LSOverride
	ehthumbs.db
	Thumbs.db

	# Icon must end with two \r
	Icon

	# Thumbnails
	._*

	# Files that might appear on external disk
	.Spotlight-V100
	.Trashes

	# Directories potentially created on remote AFP share
	.AppleDB
	.AppleDesktop
	Network Trash Folder
	Temporary Items
	.apdisk

	bower_components
	.tmp
	.sass_cache
	.codekit-cache

	# Craft specific
	craft/storage/backups/
	craft/storage/runtime/
	craft/storage/userphotos/