Ruby-Static-Site-Generator
==========================

A super basic site template generator (main purpose was to let me play around with Ruby)

##To run

Open up your terminal and enter: `rake build`

Enter in the full directory path to where you want the files created (or just press 'enter' to default to the Desktop)

Then enter the name of your project.

The rake task will then go away and create the following site structure...

- assets
    - images
    - styles
        - base.css
    - scripts
        - require.js
        - bootstrap.js
- index.html
- humans.txt

...open up the `Rakefile` file to change the structure/files created by the rake task.

##Notes

Yes you could quite easily just set-up a GitHub repo and `git clone` it whenever you need your basic site set-up, but the whole point of this repo was for me to get an excuse to play around with Ruby.

##TODO

I need to clean up the code as it's a bit of a mess. Because it's a rake task it doesn't need full blown Object-Oriented code design but it could be a lot better.
