Braid Documentation:
(c) Unstable Egghead 2013

This is a modified variant of the twee compiler from the twine interactive
fiction game creation engine. It has a number of new features and tweaks that
are different from the original twee compiler, which hasn't been maintained in
a number of years.

There are two primary python scripts contained within this distribution, there
is the braid compiler, and the unbraid decompiler.

The braid compiler will take a set of twee source files and output an html file
that can be viewed with a web browser to play the game. The unbraid decompiler
can take in a source html file, and output either one or a set of twee source
files.

There are also 3 targets included with the compiler by default, there is
upgraded versions of the sugarcane and jonah headers, which were taken from
https://github.com/tweecode/twine . The third header file is a variant of
sugarcane called SugarCube, with many new macros and general bugfixes by TheMadExile
which can be found at http://www.motoslave.net/sugarcube/ . SugarCube is the default
header used by braid, but it can be changed via the command line parameters.

Braid can also use, instead of command line parameters, a special configuration
file in order to have an easier time doing builds over many times. The filename
of this file must be either braid.cfg or braid.ini, and it must be located
within the directory that you execute braid. So, if for example you execute
braid from the directory ~/Documents/twine/game, then braid.cfg must be
located within the ~/Documents/twine/game directory, and not from wherever
braid is located. There is an example configuration file in the format that
braid expects and fully commented at doc/braid.cfg.example .

Passages tagged with special tags execute special functionality within the
compiler. These tags are:
   Twine.private: 
      These passages are basically viewed as comments, and will not be 
      included in the final html file.
   Twine.system:
      Same behavior as Twine.private.
   Twine.media:
      This will automatically base64 encode and embed any image files found
      within the source text. The currently supported formats are png, gif and
      jpg. Be aware that Internet Explorer prior to 8 has no support for this,
      and 8 has a 32kb filesize limit. IE9 and above do not have these
      limitations. The files must be located relative to the current directory
      when braid is executed, so if it is executed in ~/Documents/twine/game,
      image.png would designate ~/Documents/twine/game/image.png .
   Twine.debug:
      Passages marked with this tag will not strip newline characters from the
      passage text, this means that within the HTML file, it does not will not
      condense it all to fit on one line. The -n newline functionality uses
      this internally to select passages to not strip of newlines.

The parameters for braid are as follows:
   -a --author:
      This sets the author for the file, which primarily shows up in the html
      source. The default for this is braid.
   -t --target:
      This specifies the target for the resulting file, this selects the header
      file in the directory listed in the targets subdirectory. The valid
      options as per default are sugarcane, sugarcube, and jonah. The default
      is sugarcube.
   -m --merge:
      This specifies a source html file that is first loaded to get its
      passages, the new passages are then appended to this, with newer versions
      replacing older versions, before the final result is then added to the
      target html file. Note that this does not include any of the source
      header html, it purely loads in the passages from the source.
   -l --list:
      This specifies a file that is loaded that can contain a list of twee
      source files to load. This accepts globbing such as *.tw or game/*. Note
      that these are relative to the directory in which the script is executed.
      If you are in for example /home/james/twine, a file of test.tw will be
      /home/james/twine/test.tw. One file is specified per line.
   -o --output:
      By default, braid will output to standard output the resulting html file,
      which can be piped into a file or whatever, in the form of:
         braid test.tw > test.html
      This option specifies a file that the output is instead written to, note
      that the file will be overwritten, so take care to select a valid file.
   -d --directory:
      This specifies a directory that braid will then change to and recurse
      through, grabbing any twee source file that it finds and adding it to
      its list of files to load. So for example braid -d . will load all the
      files within the current directory and any subsequent subdirectories with
      the .tw file extension. This extension can be changed in the next option.
   -f --filetype:
      This specifies the file extensione that braid looks for during -d execution. By
      default this is .tw, but it can be changed to anything else with this
      parameter. Note that you should include the full extension in the string,
      such as -f ".txt" in order to find all .txt files and load them.
   -e --errorfile:
      This specifies a file that braid will output any errors to, thouggh this
      option by default will not write anything to the file, for that there are
      two options that must be given. They are:
         -b --brokenlink:
            Braid will traverse the entire passage list and pull any links from
            them. It will then check to see whether there are any broken links
            that are not linked to an existing passage, and will also output
            any orphan passages that have nothing linking to them. This
            supports all forms of linking between passages in the default
            sugarcube header. Check the documentation for their usage. 
            These include the following:
               - Standard Links
               - Image Links
               - Action Macros
               - Choice Macros
               - Display Macros
               - Bind Macros
               - Link Macros
               - Return Macros
               - Back Macros
         -r --revpassage:
            This option tells braid to output the entire reverse passage list,
            which is the passages linked to a particular passage. As this can
            be quite large on complex games, it is not recommended to do this
            at all times, but it could prove to be useful information to see
            what is linked to a certain passage.
   -p --proofcopy:
      This tells braid to output an rtf file to the file given. This file is
      a proofing copy which has formatting to better show passages and their
      tags. This is primarily used to ease with proofreading of the game
      itself. The various links and macros have their own special formatting
      such as italics and underlines.
   -n --newlines:
      If set, braid will skip stripping newlines on passages containing either
      the tagname given, or if all, will not strip newlines for all passage
      types.

The parameters for unbraid are as follows:
   -a --author:
      Same as for braid, setting the author field, but it doesn't seem to have
      much effect.
   -o --output:
      Same as for braid, by default it will output the source twee file to
      standard output. This specifies a file to output this to instead.
   -r --rtf:
      Outputs an rtf proofing copy of the source html file, same as for braid.
   -s --splitdir:
      This specifies a directory that unbraid will output split twee source code
      files to. By default, unbraid will output one single file containing all
      passages contained in the source html file. For projects containing many
      passages, this may be less than ideal. This flag causes braid to output
      twee source code files for each passage, named after the passage names.
      There are a few caveats for using this mode, one is that it does not
      overwrite the files it writes to, this is because some passage names
      become the same after altering them to make them more suitable for file
      names. Overwriting them otherwise would cause some passages to be lost,
      which is not ideal. So running this mode on the same directory after
      running it once will result in files that will have double the content.
   -f --filetype:
      Specifies what braid should set as the file extension when using splitdir
      mode. Defaults to .tw, but can be changed to anything else, same as for
      braid.
