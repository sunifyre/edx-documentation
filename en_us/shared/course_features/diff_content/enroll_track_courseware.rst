.. _Enrollment Track Specific Courseware Overview:

#######################################################
Creating Course Content for Specific Enrollment Tracks
#######################################################

This section provides information about setting up content for specific
enrollment tracks.

.. contents::
  :local:
  :depth: 1

*********
Overview
*********

If your course has more than one enrollment track, you can create different
course experiences for learners in different cohorts.

You do this by designating specific components in your course as visible only
to learners in one or more of the enrollment tracks. Then, learners in each of
the enrollment tracks see only the course content that you have designated for
that track.

Complete these steps to create track-specific content in your course.

#. Specify course components as visible only to particular enrollment tracks
#. View units designated for each enrollment track
#. Preview track-specific course content


.. _About Enrollment Tracks:

********************
Enrollment Tracks
********************

Implications of learners changing tracks - what happens to their current score and progress? Is it kept? Visibility of course content will change?
Cannot upgrade to verified track beyond the deadline but no limitation on dropping back to audit track?


.. _View Usage of an Enrollment Track:

*************************************
View Usage of an Enrollment Track
*************************************

To view the units that are visible to learners in an enrollment track, follow
these steps.

#. In Studio, select **Settings**, then select **Group Configurations**.

#. On the **Group Configurations** page, locate the enrollment track for which you
   want to view the usage. Enrollment tracks are only shown on this page if more than one exists in the course.

   The enrollment track's box displays the number of units that are designated for learners in the track, and provides links to each unit.

#. Select each link to go to that unit in the **Course Outline**.

For details about previewing your course to ensure that learners in an enrollment track correctly see the content intended for them, see XXXX



.. _Specify Components as Visible Only to Certain Enrollment Tracks:

******************************************************************
Specify Components as Visible Only to Particular Enrollment Tracks
******************************************************************

You can edit your course in Studio and modify the visibility settings of
components that you want to make visible only to learners in particular
enrollment tracks.

You can specify content as visible to learners by enrollment track only at the
component level in a unit. You cannot specify entire units, subsections, or
sections for visibility by enrollment track.

.. note:: You do not need to edit the visibility settings of components that
   are intended for all learners. Components that you do not explicitly
   indicate as visible to a group are visible to all learners enrolled in your
   course, regardless of their enrollment track.


To specify components as visible only to learners in particular enrollment
tracks, follow these steps.

#. In Studio, select **Content**, then select **Outline**.

#. In each unit, for each component that you want to make visible only to a
   particular enrollment track, select the **Visibility Settings** icon.

   .. image:: ../../../../shared/images/Cohorts_VisibilitySettingInUnit.png
    :alt: A component in the unit page with the visibility setting icon
      highlighted.
    :width: 600

#. In the **Editing visibility** dialog box, select **Enrollment Tracks** in
   the **Change visibility to** control, then select the checkbox for each
   enrollment track for which you want the current component to be visible.

   .. image:: ../../../../shared/images/visible_to_enrollment_track.png
    :width: 400
    :alt: The visibility settings dialog box for a component, showing
      enrollment tracks selected as the option for visibility, and the
      "verified" enrollment track selected,

#. Select **Save**.

The **Visibility Settings** icon for the component is now black.

.. image:: ../../../../shared/images/Cohorts_VisibilitySomeGroup.png
   :alt: The black visibility icon for a component, showing that the component
     is restricted
   :width: 200

The publishing details for the course section in the sidebar indicate that some
content is visible only to specific groups of learners.

.. image:: ../../../../shared/images/Content_OnlyVisibleToParticularGroups.png
   :alt: Course outline sidebar showing showing a black unit visibility icon
     and the note indicating that some content in the unit is visible only to a
     particular group.
   :width: 300

For details about previewing your course to ensure that learners in a
particular enrollment track correctly see the content intended for them, see
:ref:`XXXXXX`.

.. note:: In addition to visibility settings for content groups, a learner's
   ability to see a course component also depends on whether it is marked as
   visible to staff only, whether the unit is published, and the course's
   release date. For details about testing course content in general, see
   :ref:`Testing Your Course Content`.


.. _Viewing Track Specific Courseware:

**************************************
Viewing Track-Specific Course Content
**************************************

After you designate components as being visible only to the learners in
certain enrollment tracks, you can view your course content as a learner in
each affected enrollment track to ensure that learners in each track correctly
see the content intended for them.

.. note:: In addition to visibility settings for content groups, a learner's
   ability to see a course component also depends on whether it is marked as
   visible to staff only, whether the unit is published, and the course's
   release date. For details about viewing course content in various publishing
   states, see :ref:`View Published Content` and :ref:`Preview Unpublished
   Content`.

Depending on whether you want to view published content or unpublished content,
you choose either **View Live** or **Preview** from the course outline in
Studio. You can then experience the course content as a learner in a particular
group would, by selecting the **View this course as** option for a learner in
the desired enrollment track, as described in :ref:`Roles for Viewing Course
Content`.

For details about testing course content, see :ref:`Testing Your Course
Content`.
