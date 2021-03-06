Concepts and core objects
=========================

.. py:currentmodule:: clubsandwich.ui

The Scene
---------

To render a UI, you need to use a :py:class:`UIScene`::

  from clubsandwich.director import DirectorLoop
  from clubsandwich.ui import (
      UIScene,
      ButtonView,
  )
  class MainMenuScene(UIScene):
      def __init__(self, *args, **kwargs):
          views = [ButtonView(text="Quit", callback=self.quit)]
          super().__init__(views, *args, **kwargs)

      def quit(self):
          self.director.pop_scene()

  class GameLoop(DirectorLoop):
      def get_initial_scene(self):
          return MainMenuScene()

  GameLoop().run()

.. autoclass:: clubsandwich.ui.UIScene
  :members:

Layout
------

Layouts, most of the time, are specified using
:py:class:`~clubsandwich.view.layout_options.LayoutOptions` objects. These more
or less follow the UIKit springs-and-struts model. Here's a diagram::

  +----------------------------+
  |             ^              |
  |            top             |
  |             v              |
  |        +---------+         |
  |        |       ^ |         |
  |        |       | |         |
  |<-left->|<-width->|<-right->|
  |        |       | |         |
  |        |   height|         |
  |        |       v |         |
  |        +---------+         |
  |             ^              |
  |          bottom            |
  |             v              |
  +----------------------------+

Each listed metric can either be a *spring* or a *strut*. Struts are exact
values based on either a constant or a percentage of the superview. Springs
are totally unconstrained, and their values are derived after the struts have
been enforced.

In clubsandwich, springs are represented by ``None``. All the other values
define some kind of strut:

* Ints >= 1 are constants.
* Floats from 0 (inclusive) to 1 (exclusive) are fractions of the superview's
  width or height.
* The string ``intrinsic`` means "at layout time, take the value from my
  :py:attr:`View.intrinsic_size` property." This is useful when you don't know
  in advance how big your content is.
* The string ``frame`` means "at layout time, take the value from the
  corresponding attribute of my :py:attr:`View.frame` property."

So to put something 3 cells from the left that is 50% as wide as its
superview, you'd set ``left`` to ``3`` and ``width`` to ``0.5``. To fill the
superview (this is the default), set left/right/top/bottom to ``0`` and
width/height to ``None``.

There is a special case: when ``width`` is set but ``left`` and ``right`` are
``None``, the view is centered horizontally. It works the same way for the
vertical axis.

The :py:class:`LayoutOptions` class has several convenience initializers and
methods to make these specs extremely concise.

.. py:class:: LayoutOptionValue

  This is not a real class, but in these docs, it represents the possible
  values for the attributes of :py:class:`LayoutOptions`.

  * ``None``: Do not constrain this value. It is a spring.
  * ``0.0-1.0`` left-inclusive: Use a fraction of the superview's size on the
    appropriate axis.
  * ``>=1``: Use a constant integer
  * ``'intrinsic'``: The view defines an ``intrinsic_size`` property; use this
    value. Mostly useful for ``LabelView``.
  * ``'frame'``: Derive a constant from the *initial* frame of this view. This
    initial frame is stored in :py:attr:`View.layout_spec`, so
    if you need to change it, you can just change that attribute.

.. autoclass:: clubsandwich.ui.layout_options.LayoutOptions
  :members:

Views
-----

.. autoclass:: clubsandwich.ui.View

Positioning
~~~~~~~~~~~

.. autoattribute:: clubsandwich.ui.View.bounds

.. autoattribute:: clubsandwich.ui.View.frame

.. autoattribute:: clubsandwich.ui.View.scene

.. autoattribute:: clubsandwich.ui.View.intrinsic_size

View hierarchy
~~~~~~~~~~~~~~

.. autoattribute:: clubsandwich.ui.View.superview

.. automethod:: clubsandwich.ui.View.add_subviews

.. automethod:: clubsandwich.ui.View.remove_subviews

Layout
~~~~~~

.. automethod:: clubsandwich.ui.View.set_needs_layout

.. automethod:: clubsandwich.ui.View.layout_subviews

Drawing
~~~~~~~

.. automethod:: clubsandwich.ui.View.draw

First Responder
~~~~~~~~~~~~~~~

You might want to read about :py:class:`FirstResponderContainerView`
before diving into this section.

.. automethod:: clubsandwich.ui.View.terminal_read

.. autoattribute:: clubsandwich.ui.View.can_become_first_responder

.. automethod:: clubsandwich.ui.View.did_become_first_responder

.. automethod:: clubsandwich.ui.View.descendant_did_become_first_responder

.. autoattribute:: clubsandwich.ui.View.can_resign_first_responder

.. automethod:: clubsandwich.ui.View.did_resign_first_responder

.. automethod:: clubsandwich.ui.View.descendant_did_resign_first_responder

.. autoattribute:: clubsandwich.ui.View.first_responder_container_view

Tree traversal
~~~~~~~~~~~~~~

.. autoattribute:: clubsandwich.ui.View.leftmost_leaf

.. autoattribute:: clubsandwich.ui.View.postorder_traversal

.. autoattribute:: clubsandwich.ui.View.ancestors

.. automethod:: clubsandwich.ui.View.get_ancestor_matching

Debugging
~~~~~~~~~

.. autoattribute:: clubsandwich.ui.View.debug_string

.. automethod:: clubsandwich.ui.View.debug_print

First Responder
---------------

.. autoclass:: clubsandwich.ui.FirstResponderContainerView
  :members: