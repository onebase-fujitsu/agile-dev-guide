# Fujitsu Agile Development Guide

![build](https://img.shields.io/badge/build-passing-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-green?style=flat-square)
![go-lang](https://img.shields.io/badge/Go-^1.16.6-76E1FE.svg?logo=go&style=flat-square)
![Hugo](https://img.shields.io/badge/Hugo-%5E0.87.0-ff4088?style=flat-square&logo=hugo)

This is the development repository for Fujitsu agile development guide.

## How to read 📕

You can read this [here](https://onebase-fujitsu.github.io/agile-dev-guide/).

## How to develop 🛠

We are currently not accepting contributions from outside Fujitsu.

1. install Go  

   Download [go-lang sdk](https://golang.org/dl/) and install.

2. install Hugo

   Follow the instructions on the [Hugo](https://gohugo.io/getting-started/installing/) website for installation.

3. launch development server

   After moving to the working directory...
   
    ```shell
    hugo server -D
    ```
   
4. browse [http://localhost:1313/agile-dev-guide/](http://localhost:1313/agile-dev-guide/) 🚀

## How to deploy 📦

Just commit the changes to the master branch and push the changes to this repository.
Deployment to the public site is done automatically by GitHub Actions.👋

## License 🔑

This guide book and its source code are licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).

## Author 🖋

Takayuki Tominaga, Fujitsu Ltd.
