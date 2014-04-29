DC Code XML
===========

This repository contains the Code of the District of Columbia in XML format.

The DC Code in XML makes use of a custom schema that draws on ideas from [Akoma Ntoso](http://www.akomantoso.org/) and [United States Legislative Markup](http://uscodebeta.house.gov/download/download.shtml).

The schema can almost entirely be seen in the following four examples.

(1) Root element. The root element is a `<level>` element. It contains some metadata and nested `<level>` elements for the first level divisions of the code.

	<level>
		<type>document</type>
		<heading>Code of the District of Columbia</heading>

		<meta>
			<recency>current through DC Act 19-658; unofficial through D.C. Act 19-682</recency>
		</meta>

		<!-- <level> elements as shown below -->

	</level>

(2) "Big" levels that appear in the Table of Contents (`toc`). These are divisions, titles, chapters, and so on above the level of a section.

    <level>
		<type>toc</type>
		<prefix>Title</prefix>
		<num>1</num>
		<heading>Government Organization</heading>

		<!-- other <level> elements nested here -->

    </level>

(3) Sections and annotations.

	<level>
		<type>section</type>
		<num>2-531</num>
		<heading>Public policy.</heading>

		<text>The public policy of the District of Columbia is that all persons are entitled
		to full and complete information regarding the affairs of government and the official
		acts of those who represent them as public officials and employees. To that end,
		provisions of this subchapter shall be construed with the view toward expansion of public
		access and the minimization of costs and time delays to persons requesting information.</text>

		<level>
		  <type>annotations</type>
		  <level>
		    <heading>History</heading>
		    <text>(Oct. 21, 1968, Pub. L. 90-614, title II, ...</text>
		  </level>
		</level>
	</level>

(4) "Little" levels which are rendered as paragraphs (often numbered).

	<level>
		<num>(c)</num>
		<heading>United States</heading>

		<text>The United States, its officers, employees, and agents, and its agencies shall not:</text>

		<level>
			<num>(1)</num>
			<text>Be responsible for the payment of any judgments, liabilities or costs resulting
			from any action or proceeding against the District of Columbia or its agencies,
			officers, employees, or agents;</text>
		</level>

		<!-- other <level> elements nested here -->
	</level>

XInclude
--------

The Code begins in [index.xml](index.xml) but is broken apart into many small files using XInclude. Any `<level>` element may be replaced by an XInclude element referencing another file, using a relative path, whose root node semantically belongs at the location of the XInclude.

We split the code at titles, chapters, sections, and so on. An XInclude element never appears within a section or placeholder.

The top-level `index.xml` file looks like this:

	<level>
	  <type>document</type>
	  <heading>Code of the District of Columbia</heading>
	  <meta>
	    <recency>current through DC Act 19-658; unofficial through D.C. Act 19-682</recency>
	  </meta>
	  <level>
	    <type>toc</type>
	    <prefix>Division</prefix>
	    <num>I</num>
	    <heading>Government of District</heading>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-1/index.xml"/>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-2/index.xml"/>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-3/index.xml"/>
	    ...
	  </level>
	  <level>
	    <type>toc</type>
	    <prefix>Division</prefix>
	    <num>II</num>
	    <heading>Judiciary and Judicial Procedure</heading>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-11/index.xml"/>
	    <ns0:include xmlns:ns0="http://www.w3.org/2001/XInclude" href="Title-12/index.xml"/>
	    ...
	</level>

The namespace prefix `ns0` is subject to change.

Root Element and Metadata
-------------------------

The root element is a `<level>`, like most other nodes in the schema. We are not using namespaces.

The root element contains a `<meta>` element with document metadata. The `<meta>` element is followed by `<level>` elements representing the Divisions of the Code. In the actual root file `index.xml` in this repository some of the `<level>` elements shown below are replaced by XIncludes, but we show them replaced with their referenced content here to highlight the intended semantics.

	<?xml version='1.0' encoding='utf-8'?>
	<level>
	  <type>document</type>
	  <heading>Code of the District of Columbia</heading>
	  <meta>
	    <recency>December 13, 2013 and through D.C. Act 20-210 (except D.C. Acts 20-130, 20-157, and 20-204)</recency>
	  </meta>
	  <level>
	    <type>toc</type>
	    <prefix>Division</prefix>
	    <num>I</num>
	    <heading>Government of District</heading>
	    <level>
	      <type>toc</type>
	      <prefix>Title</prefix>
	      <num>1</num>
	      <heading>Government Organization</heading>
	      <level>
	        <type>toc</type>
	        <prefix>Chapter</prefix>
	        <num>1</num>
	        <heading>District of Columbia Government Development</heading>
	        <level>
	          <type>toc</type>
	          <prefix>Subchapter</prefix>
	          <num>I</num>
	          <heading>District of Columbia Establishment</heading>
	          <level>
	            <type>section</type>
	            <num>1-101</num>
	            <heading>Territorial area.</heading>
	            <text>The District of Columbia is that portion of the territory
	            of the United States ceded by the State of Maryland for the
	            permanent seat of government of the United States, including the
	            river Potomac in its course through the District, and the islands
	            therein.</text>

Levels
------

All hierarchy of the Code is represented by `<level>` elements. This includes both "big" levels (titles, chapters, etc.), sections, and "little" levels (subsections, paragraphs, and so on).

A `<level>` contains any of the following subelements:

* `<type>`: The text in this element indicates the type of the level, as follows:
 * `document`: The root of the entire document. There is one such element and it is at the top of the hierarchy.
 * `toc`: These are divisions, titles, chapters, and so on above the level of a section. `toc` is a mnemonic for Table of Contents. These levels appear in the Table of Contents of the code.
 * `section`: Sections of the code (e.g. § 1-101), the primary level at which text and annotations occur and what citations target.
 * `placeholder`: The level is section-like but it represents Code sections that no longer exist. They are documented specifically in a section below.
 * `annotations`: Annotation blocks.
 * `appendices`: Appendices to a section (typically but not always within `Section`-levels).
 * `form` and `table`: Blocks that are rendered with internal whitespace preserved. `form` contains quoted language that the Code is directing to be used on government forms. `table` contains pay schedules and other tables that are layed out in a monospace font.
 * When `<type>` is omitted, the level is a numbered paragraph (i.e. `(1) The public policy....`) or an unnumbered block with a heading. Inside annotations, subheadings are represented this way.

* `<prefix>`: Used in `toc` levels only to provide the text that appears before the level's number. Possibly values are `Article`, `Chapter`, `Division`, `Part`, `Subchapter`, `Subdivision`, `Subpart`, `Subtitle`, `Title`, and `Unit`.
* `<num>`: The level's number. 
 * For `toc` levels, this usually is a number, letter, or roman numeral.
 * For sections, the number is roughly in the form `1-101` (title, hyphen, section number). It never includes the §-symbol.
 * For "little" levels, this is the paragraph's numbering including any parenthesis around the numbering. An example would be `(12A-i)`.
 * `<num>` isn't ever required. When `<num>` is omitted, the level is simply unnumbered. `document` and `placeholder` levels never have `<num>` elements. For placeholders, see below.

* `<heading>`: The level's name or heading.
 * For `toc` levels, sections, placeholders, and subheadings in annotations, this is the name of the level, such as `Government Organization`.
 * A heading can also appear on "little" levels and would appear as something like italicized text.

* `<text>` and `<level>` content elements.
 * Zero or more `<text>` and `<level>` elements can appear within a `<level>` and should be rendered in document order.
 * Multiple `<text>` elements are rendered as separate paragraphs. If a paragraph `<level>` has a `<num>` and/or `<heading>`, it is usually rendered inside and at the start of the first `<text>` paragraph.

Text
----

Text content is contained within `<text>` elements inside `<level>`s. `<text>` may be interleaved with other `<text>` and `<level>` elements and should be rendered in document order. Each `<text>` element is a separate paragraph. These elements contain HTML, e.g.:

	<text>One <span style="font-style: italic; ">ex officio </span>Commissioner,
	the Deputy Mayor for Planning and Economic Development;</text>

An optional `class` attribute can be one of:

* `centered`: The paragraph is rendered with centered text alignment.

Inside the `<text>` element is HTML conforming to the following restrictions:

* Only bare text and `<span>` elements may appear.
* `<span>` elements may have a style attribute containing only the following CSS:
 * `font-weight: bold;`
 * `font-style: italic;`
 * `text-decoration: underline;`
 * `font-family: monospace;`

Placeholders
------------

Placeholder levels are similar to levels with `type`=`section` but represent Code sections that no longer exist. Here is an example:

	<level>
	  <type>placeholder</type>
	  <reason>Not funded</reason>
	  <section>1-136.01</section>
	  <heading>Establishment of the 51st State Commission.</heading>
	  <text>[Not funded].</text>
	  <level>
	    <type>annotations</type>
	    ...

These levels do not have a `<num>` element. The `<heading>` element typically contains the name of the section that was removed. These levels may also have these other child elements:

* `<reason>`: The reason for the placeholder.
 * When present, one of `Expired`, `Not funded`, `Omitted`, `Repealed`, `Reserved`, and `Transferred`.
 * When not present, the reason for the placeholder is not indicated in the code.
* `<section>`: The number of the omitted section (e.g. `1-136.01`).

In place of `<section>`, a placeholder may instead contain:

* `<section-start>`: The number of the first omitted section in a range of omitted sections.
* `<section-end>`: The number of the last omitted section in a range of omitted sections.
* `<section-range-type>`: This element contains the text `range` if all sections between start and end are omitted (usually rendered as "A to B") or `list` if the start and end name two sections but a range is not implied (usually rendered as "A, B").

