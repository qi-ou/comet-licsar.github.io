.. .. include:: links.rst

Notes about documentation
=============

Documentation can be updated simply by editing the rst files in the 'source' folder. After commit, github would auto-recreate web page of https://comet-licsar.github.io .

these are some testing lines

symbol $

symbol \$


links are automatically converted, e.g. as:

  https://gitlab.com/comet_licsar/licsar_documentation/-/wikis/home.


Admin or dev comments
=====================

How do I..
----------

clone and edit the docs
^^^^^^^^^^^^^^^^^^^^^^^

few steps are needed to clone and set the docs on your computer. first of all, you should set in github page, in Settings of your profile an up-to-date ssh key, formed with the email that is registered with your profile (you may add more emails in github profile settings, it works).

you may do e.g.
::
  ssh-keygen -t ed25519 -f ~/.ssh/id_ed -C "YOURUSERNAME@leeds.ac.uk"

then edit your ~/.ssh/config to contain:
::
  Host github.com
    IdentityFile ~/.ssh/id_ed
    IdentitiesOnly yes

Afterwards, you can just enter some directory and clone (and set) the repo there using:
::
  git clone git@github.com:comet-licsar/comet-licsar.github.io.git
  cd comet-licsar.github.io
  git remote set-url origin git@github.com:comet-licsar/comet-licsar.github.io.git

ok, then you're set and you can edit and push changes (if you are maintainer) as usual.


prepare hi res frames
^^^^^^^^^^^^^^^^^^^^^

say we have an AOI as e.g.

::

  import framecare as fc
  lat1,lon1 = 51.1, 15.7
  lat2,lon2 = 51.8, 16.5
  
  # first get bursts over that AOI, e.g. as:
  bursts = fc.lq.get_bursts_in_polygon(lon1,lon2,lat1,lat2)
  bursts = fc.lq.sql2outlist(bursts)
  
  # now manually check the bursts and select only 1 relorb related..
  bursts = ....
  
  # then generate high res frame with the AOI
  fc.generate_new_frame(bursts, testonly=False, hicode='H')
  

Afterwards initialize the new frame with parameter -C, i.e.:

::

   licsar_initialize_frame.sh -C 15.7/16.5/51.1/51.8 022A_0142H_000203


prepare hi res subframes
^^^^^^^^^^^^^^^^^^^^^^^^

I know this sounds as duplicate - but it is not.
While the above commands would create new hires frame (that will be normally processed through licsar_make_frame.sh etc.),
it is often much better just to crop existing large frame, as this will ensure correct SD estimation, and generally decrease
doubled processing needs.

Now, this is finally prepared in the way that once you initialise such subset (as shown below),
the RSLCs will then automatically get updated/clipped once the frame is processed and you run `store_to_curdir.sh`.

The frame subsets will reside in $LiCSAR_procdir/subsets

Let me show the current approach through real example..

Due to earthquake near to SAREZ dam (!!!! a nightmare for middle East for the last 100 years), John Elliott requested hires frames.
He defined the region as (lons/lats): 72.510/72.845/38.130/38.365.
Thus, let's get frames and initialise their subsets using:

::

  import framecare as fc
  frames=fc.lq.get_frames_in_lonlat(72.7,38.2)
  # returns: (('005D_05199_131313',), ('100A_05236_141313',))
  for frame in frames:
      fc.subset_initialise_corners(frame, 72.510, 72.845, 38.130, 38.365, 'SAREZ')

and that's it. Now you may proceed just to process the frames as usual.
Note that this will not generate interferograms. But this can be done simply by running `framebatch_gapfill.sh`
(more instructions later, or ask Lin Shen for advice).

If this is only one-off procedure (no need to store in `subsets` folder), you may also just 
run in their `$BATCH_CACHE_DIR/$frame` following command that will also generate interferograms:

::

   clip_slc.sh SAREZ_005D 72.510 72.845 38.130 38.365 0 0.00027 1


test
----

this is section
^^^^^^^^^^^^^^^

`this is quoted text`

Examples
~~~~~~~~

Some example of Sphinx generated documentation

* bullet 1
* bullet 2

link to `Sphinx Examples`_


ok, now this is from RSTPAD:

h1
##

h2
**

# h3
# ==

h4
--

h5
^^

h6
""

**bold**
*italic*
- list
#. numlist
something

s ---- thing

  tab
`link <http://example.com/>`_
.. image:: image
inline ``code is ``this

code block ::

  is
  this block
  is this

highlighted

.. code:: python3

  block
  of
  code
  is
  this

tralala

The library can also be installed using ``pip``::

  pip install sphinx

The rst format was developed for writing documentation. There are numerous markup options available such as using asterisks to emphasise text::

  *emphasised text*

will be rendered as:

*emphasised text*

code example:

.. code:: python

  def function_a(arg_a, arg_b):
      """
      This is function a, which adds two values 
  
      :param arg_a: first argument is a ``float``
      :param arg_b: second argument in an ``int``
      :return: arg_a + arg_b
  
      Example usage::
  
        >>> from python_library import function_a
        >>> function_a(2, 3)
        5
      """
      return arg_a + arg_b

This will produce documentation which renders as:

.. py:function:: python_library.function_a(arg_a, arg_b)
  :noindex:

  This is function a, which adds two values

  :param arg_a: first argument is a ``float``
  :param arg_b: second argument in an ``int``
  :return: arg_a + arg_b

  Example usage::

    >>> from python_library import function_a
    >>> function_a(2, 3)
    5

To include autogenerated documentation, there are various methods available (see the `Sphinx Autodoc Documentation`_), for example, to include documentation for all members of the Python library ``python_library``::

  .. automodule:: python_library
      :members:

this::

  $ make 
  Sphinx v4.4.0
  Please use `make target' where target is one of
    html        to make standalone HTML files
    dirhtml     to make HTML files named index.html in directories
    singlehtml  to make a single large HTML file
  ...

For example, to build HTML documentation, run::

  make html


