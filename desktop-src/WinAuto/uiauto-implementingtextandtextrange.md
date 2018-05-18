---
title: Text and TextRange Control Patterns
description: Describes guidelines and conventions for implementing ITextProvider, ITextProvider2, and ITextRangeProvider, including information about properties and methods.
ms.assetid: '4d541c31-11f7-4d7e-a0e0-9ed1da873d07'
keywords: ["UI Automation,implementing Text control pattern", "UI Automation,implementing TextRange control pattern", "UI Automation,Text control pattern", "UI Automation,TextRange control pattern", "UI Automation,ITextProvider", "UI Automation,ITextRangeProvider", "ITextProvider", "ITextRangeProvider", "implementing UI Automation Text control pattern", "implementing UI Automation TextRange control pattern", "Text control pattern", "TextRange control pattern", "control patterns,ITextProvider", "control patterns,ITextRangeProvider", "control patterns,implementing UI Automation Text", "control patterns,implementing UI Automation TextRange", "control patterns,Text", "control patterns,TextRange", "interfaces,ITextProvider", "interfaces,ITextRangeProvider"]
---

# Text and TextRange Control Patterns

Describes guidelines and conventions for implementing [**ITextProvider**](uiauto-itextprovider.md), [**ITextProvider2**](https://msdn.microsoft.com/library/windows/desktop/hh448818), and [**ITextRangeProvider**](uiauto-itextrangeprovider.md), including information about properties and methods. The **Text** control pattern enables applications and controls to expose a simple text object model, enabling clients to retrieve textual content, text attributes, and embedded objects from text-based controls.

To support the **Text** control pattern, controls implement the [**ITextProvider**](uiauto-itextprovider.md) and [**ITextProvider2**](https://msdn.microsoft.com/library/windows/desktop/hh448818) interfaces. Control types that should support the **Text** control pattern include the [Edit](uiauto-supporteditcontroltype.md) and [Document](uiauto-supportdocumentcontroltype.md) control types, and any other control type that enables the user to enter text or select read-only text.

The **Text** control pattern can be used with other Microsoft UI Automation control patterns to support several types of embedded objects in the text, including tables, hyperlinks, and command buttons.

The [**ITextProvider**](uiauto-itextprovider.md) and [**ITextProvider2**](https://msdn.microsoft.com/library/windows/desktop/hh448818) interfaces include a number of methods for acquiring text ranges. A *text range* is an object that represents a contiguous span of text—or multiple, disjoint spans of text—in a text container. One **ITextProvider** method acquires a text range that represents the entire document, while others acquire text ranges that represent some portion of the document, such as the selected text, the visible text, or an object embedded in the text.

A text range object is represented by the **TextRange** control pattern, which is implemented through the [**ITextRangeProvider**](uiauto-itextrangeprovider.md) interface. The **TextRange** control pattern provides methods and properties used to expose information about the text in the range, move the endpoints of the range, select or deselect text, scroll the range into view, and so on.

For more information about the **Text** and **TextRange** control patterns, see [UI Automation Support for Textual Content](uiauto-ui-automation-textpattern-overview.md).

Starting with Windows 8.1 providers can implement the [**ITextRangeProvider2**](uiauto-itextrangeprovider2.md) interface. This enables invoking context menus that are associated with a text range. This supports scenarios such as text autocorrection or Input Method Editor (IME) candidate selection.

This topic contains the following sections.

-   [Implementation Guidelines and Conventions](#implementation-guidelines-and-conventions)
-   [Required Members for **ITextProvider**](#required-members-for-itextprovider)
-   [Required Members for **ITextRangeProvider**](#required-members-for-itextrangeprovider)
-   [Supporting Text Ranges](#supporting-text-ranges)
    -   [Manipulating a Text Range by Text Unit](#manipulating-a-text-range-by-text-unit)
    -   [Selecting Text in a Text Range](#selecting-text-in-a-text-range)
    -   [Acquiring Text from a Text Range](#acquiring-text-from-a-text-range)
    -   [Implementing ShowContextMenu](#implementing-showcontextmenu)
-   [Interoperability with the System Caret](#interoperability-with-the-system-caret)
-   [Related topics](#related-topics)

## Implementation Guidelines and Conventions

When implementing the **Text** control pattern, note the following guidelines and conventions:

-   Any control that enables access to text (for example, entering text or selecting read-only text) should support the **Text** control pattern.
-   The **Text** control pattern can be used with any UI element that presents text, even a static label on a standard button control. However, it is not required on static text controls that cannot be selected or do not have an insertion point.
-   To ensure that text is fully accessible, controls that implement [**ITextProvider**](uiauto-itextprovider.md) should also support the [**IValueProvider**](uiauto-ivalueprovider.md) interface. **IValueProvider** complements **ITextProvider** by providing a programmatic way to change the text. It also offers greater compatibility with assistive technology client applications, including those based on legacy technologies such as Microsoft Active Accessibility. When both control patterns are implemented, the **TextChanged** event ([**UIA\_Text\_TextChangedEventId**](uiauto-event-ids.md#uia-text-textchangedeventid)) and **AutomationPropertyChanged** event ([**UIA\_AutomationPropertyChangedEventId**](uiauto-event-ids.md#uia-automationpropertychangedeventid)) are equivalent for the **Value** property ([**UIA\_ValueValuePropertyId**](uiauto-control-pattern-propids.md#uia-valuevaluepropertyid)). Both events must be supported.
-   The **Text** control pattern supports only one stream of text and one viewport per control. If the application offers multiple views of document in panes, each view (control) should support [**ITextProvider**](uiauto-itextprovider.md) independently.
-   The [**ITextProvider::GetSelection**](uiauto-itextprovider-getselection.md) method may return a single text range representing the currently selected text. If a control supports the selection of multiple, noncontiguous spans of text, the **GetSelection** method should return an array that contains one [**ITextRangeProvider**](uiauto-itextrangeprovider.md) interface for each selected span of text.
-   The **Text** control pattern represents the insertion point as a degenerate (empty) text range. The [**ITextProvider::GetSelection**](uiauto-itextprovider-getselection.md) method should return a degenerate text range when the insertion point exists and no text is selected. For more information, see [Interoperability with the System Caret](#interoperability-with-the-system-caret).
-   The [**ITextProvider::GetVisibleRanges**](uiauto-itextprovider-getvisibleranges.md) method may return a single text range if a contiguous range of text is visible in the viewport, or it may return an array of disjoint text ranges representing multiple, partially visible lines of text.
-   The [**ITextProvider::RangeFromChild**](uiauto-itextprovider-rangefromchild.md) method should return a degenerate range if the child element contains no text. Because an embedded object can include text, the **RangeFromChild** method may not always return a degenerate text range. For more information, see [How UI Automation Exposes Embedded Objects](uiauto-textpattern-and-embedded-objects-overview.md).
-   The [**ITextProvider::RangeFromPoint**](uiauto-itextprovider-rangefrompoint.md) method performs hit testing in the document area using the specified screen coordinates. The resulting text range should be consistent with the insertion point or selection that would result from clicking the location at the specified screen coordinates. For example, if an image resides at the specified screen coordinates, the resulting text range should be the same as the text range that the [**ITextProvider::RangeFromChild**](uiauto-itextprovider-rangefromchild.md) method would acquire for the image. Similarly, if a client application requests a text range for the location at the center of the system caret (the insertion point), the resulting text range should be the same as the system caret location.
-   The [**ITextProvider::DocumentRange**](uiauto-itextprovider-documentrange.md) property should always provide a text range that includes all of the text supported by the corresponding [**ITextProvider**](uiauto-itextprovider.md) implementation.
-   The [**UIA\_Text\_TextChangedEventId**](uiauto-event-ids.md#uia-text-textchangedeventid) event must be raised after any text change occurs, even if the change is not visible in the viewport. For example, the provider should raise the event even if the user pastes the exact same text over the selected text.
-   The [**UIA\_Text\_TextSelectionChangedEventId**](uiauto-event-ids.md#uia-text-textselectionchangedeventid) must be raised whenever the selection of text changes, or whenever the insertion point (caret) moves among the text.

When implementing the **TextRange** control pattern, note the following guidelines and conventions:

-   All methods of the **TextRange** control pattern should perform text operations regardless of the visibility state of the text. The visibility of a text range can always be determined by querying the **IsHidden** text attribute ([**UIA\_IsHiddenAttributeId**](uiauto-textattribute-ids.md#uia-ishiddenattributeid)).
-   If possible, a provider should ensure that any text changes, such as deletions, insertions, and moves, are reflected in the associated text range objects (instances of [**ITextRangeProvider**](uiauto-itextrangeprovider.md) interface) and raise a [**UIA\_Text\_TextChangedEventId**](uiauto-event-ids.md#uia-text-textchangedeventid) event. Clients may use the event as a hint to confirm editorial changes made to the text of a control.
-   All of the text range objects used by the [**ITextRangeProvider::Compare**](uiauto-itextrangeprovider-compare.md), [**CompareEndpoints**](uiauto-itextrangeprovider-compareendpoints.md), and [**MoveEndpointByRange**](uiauto-itextrangeprovider-moveendpointbyrange.md) methods must be peers of the same **Text** control pattern implementation.
-   Although not required, the *pRetVal* value retrieved by the [**ITextRangeProvider::CompareEndpoints**](uiauto-itextrangeprovider-compareendpoints.md) method can indicate the distance, in characters ([**TextUnit\_Character**](uiauto-textunitenum.md#textunit-character)), between the two endpoints. However, client applications should not depend on the accuracy of *pRetVal* beyond its positive or negative value.
-   The [**ITextRangeProvider::ExpandToEnclosingUnit**](uiauto-itextrangeprovider-expandtoenclosingunit.md), [**Move**](uiauto-itextrangeprovider-move.md), and [**MoveEndpointByUnit**](uiauto-itextrangeprovider-moveendpointbyunit.md) methods require careful consideration of the specified text unit. For more information, see Manipulating a TextRange by Text Unit.
-   For implementation requirements related to the [**ITextRangeProvider::Select**](uiauto-itextrangeprovider-select.md), [**AddToSelection**](uiauto-itextrangeprovider-addtoselection.md), and [**RemoveFromSelection**](uiauto-itextrangeprovider-removefromselection.md) methods, see Selecting Text in a Text Range.
-   The [**ITextRangeProvider::FindText**](uiauto-itextrangeprovider-findtext.md) and [**FindAttribute**](uiauto-itextrangeprovider-findattribute.md) methods search forward or backward for a single matching text string or text attribute. They should return **NULL** if no match is found.
-   The [**ITextRangeProvider::GetAttributeValue**](uiauto-itextrangeprovider-getattributevalue.md) method must return the address acquired from the [**UiaGetReservedMixedAttributeValue**](uiauto-uiagetreservedmixedattributevalueautometh.md) or [**UiaGetReservedNotSupportedValue**](uiauto-uiagetreservednotsupportedvalueautometh.md) function if the associated attribute varies in the range, or if the attribute is not supported by the text control. The **TextRange** control pattern specification does not allow adding new text-attribute identifiers or changing how the existing attributes are defined.
-   If possible, the [**ITextRangeProvider::GetBoundingRectangles**](uiauto-itextrangeprovider-getboundingrectangles.md) method should return an array that contains one bounding rectangle for each fully or partially visible line of text in a text range. If this is not possible, the provider can return an array that contains the bounding rectangles of only fully visible lines; however, this limits a client application's ability to accurately describe how the text is being presented on the screen.
-   The [**ITextRangeProvider::GetChildren**](uiauto-itextrangeprovider-getchildren.md) method should return all child elements embedded in a text range, but it does not need to return any children of the child elements. For example, if a text range contains a table that has a number of child cells, the **GetChildren** method may return just the table element and not the cell elements. For performance or architectural reasons, a provider may not be able to expose all embedded objects that are hosted in a document in the automation tree. In this case, the provider should at least support the enumerating of child objects through the **GetChildren** method and, as an option, support the [VirtualizedItem](uiauto-implementingvirtualizeditem.md) control pattern for de-virtualization support.
-   The [**ITextRangeProvider::GetEnclosingElement**](uiauto-itextrangeprovider-getenclosingelement.md) method typically returns the text provider that supplies the text range. However, if the text provider supports child objects such as tables or hyperlinks, the enclosing element could be a descendant of the text provider. The element returned by **GetEnclosingElement** should be the element closest to the given text range. For example, if the text range is in a cell of a table, **GetEnclosingElement** should return the containing cell instead of the table element.
-   The [**ITextRangeProvider::GetText**](uiauto-itextrangeprovider-gettext.md) method should return the plain text in the range. For more information, see Acquiring Text from a Text Range.
-   Calling [**ITextRangeProvider::ScrollIntoView**](uiauto-itextrangeprovider-scrollintoview.md) should align the text in the viewport of the text control as specified by the *alignToTop* parameter. Although there is no requirement in terms of horizontal alignment, the text range should be visible both horizontally and vertically. When evaluating the *alignToTop* parameter, a provider must take into account the orientation of the text control and the flow direction of the text. For example, if *alignToTop* is **TRUE** for a vertically oriented text control containing text that flows from right to left, the provider should align the text range with the right side of the viewport.
-   When moving through a document by [**TextUnit\_Line**](uiauto-textunitenum.md#textunit-line), if the text range enters an embedded table, each line of text in a cell should be treated as a line.

## Required Members for **ITextProvider**

The following properties and methods are required for implementing the [**ITextProvider**](uiauto-itextprovider.md) interface.



| Required members                                                                                        | Member type | Notes |
|---------------------------------------------------------------------------------------------------------|-------------|-------|
| [**DocumentRange**](uiauto-itextprovider-documentrange.md)                                             | Property    | None  |
| [**SupportedTextSelection**](uiauto-itextprovider-supportedtextselection.md)                           | Property    | None  |
| [**GetSelection**](uiauto-itextprovider-getselection.md)                                               | Method      | None  |
| [**GetVisibleRanges**](uiauto-itextprovider-getvisibleranges.md)                                       | Method      | None  |
| [**RangeFromChild**](uiauto-itextprovider-rangefromchild.md)                                           | Method      | None  |
| [**RangeFromPoint**](uiauto-itextprovider-rangefrompoint.md)                                           | Method      | None  |
| [**UIA\_Text\_TextChangedEventId**](uiauto-event-ids.md#uia-text-textchangedeventid)                   | Event       | None  |
| [**UIA\_Text\_TextSelectionChangedEventId**](uiauto-event-ids.md#uia-text-textselectionchangedeventid) | Event       | None  |



 

The following additional properties and methods are required for implementing the [**ITextProvider2**](https://msdn.microsoft.com/library/windows/desktop/hh448818) interface.



| Required members                                                         | Member type | Notes |
|--------------------------------------------------------------------------|-------------|-------|
| [**GetCaretRange**](https://msdn.microsoft.com/library/windows/desktop/hh448820)         | Method      | None  |
| [**RangeFromAnnotation**](uiauto-itextprovider2-rangefromannotation.md) | Method      | None  |



 

## Required Members for **ITextRangeProvider**

The following properties and methods are required for implementing the [**ITextRangeProvider**](uiauto-itextrangeprovider.md) interface.



| Required members                                                                 | Member type | Notes |
|----------------------------------------------------------------------------------|-------------|-------|
| [**AddToSelection**](uiauto-itextrangeprovider-addtoselection.md)               | Method      | None  |
| [**Clone**](uiauto-itextrangeprovider-clone.md)                                 | Method      | None  |
| [**Compare**](uiauto-itextrangeprovider-compare.md)                             | Method      | None  |
| [**CompareEndpoints**](uiauto-itextrangeprovider-compareendpoints.md)           | Method      | None  |
| [**ExpandToEnclosingUnit**](uiauto-itextrangeprovider-expandtoenclosingunit.md) | Method      | None  |
| [**FindAttribute**](uiauto-itextrangeprovider-findattribute.md)                 | Method      | None  |
| [**FindText**](uiauto-itextrangeprovider-findtext.md)                           | Method      | None  |
| [**GetAttributeValue**](uiauto-itextrangeprovider-getattributevalue.md)         | Method      | None  |
| [**GetBoundingRectangles**](uiauto-itextrangeprovider-getboundingrectangles.md) | Method      | None  |
| [**GetChildren**](uiauto-itextrangeprovider-getchildren.md)                     | Method      | None  |
| [**GetEnclosingElement**](uiauto-itextrangeprovider-getenclosingelement.md)     | Method      | None  |
| [**GetText**](uiauto-itextrangeprovider-gettext.md)                             | Method      | None  |
| [**Move**](uiauto-itextrangeprovider-move.md)                                   | Method      | None  |
| [**MoveEndpointByUnit**](uiauto-itextrangeprovider-moveendpointbyunit.md)       | Method      | None  |
| [**MoveEndpointByRange**](uiauto-itextrangeprovider-moveendpointbyrange.md)     | Method      | None  |
| [**Select**](uiauto-itextrangeprovider-select.md)                               | Method      | None  |
| [**ScrollIntoView**](uiauto-itextrangeprovider-scrollintoview.md)               | Method      | None  |



 

The following additional properties and methods are required for implementing the [**ITextRangeProvider2**](uiauto-itextrangeprovider2.md) interface.



| Required members                                                      | Member type | Notes                                      |
|-----------------------------------------------------------------------|-------------|--------------------------------------------|
| [**ShowContextMenu**](uiauto-itextrangeprovider2-showcontextmenu.md) | Method      | See "Implementing ShowContextMenu" section |



 

The **TextRange** control pattern has no associated events.

## Supporting Text Ranges

This section describes how a provider should implement various methods of the [**ITextRangeProvider**](uiauto-itextrangeprovider.md) and [**ITextRangeProvider2**](uiauto-itextrangeprovider2.md) interfaces to support the **TextRange** control pattern.

### Manipulating a Text Range by Text Unit

The [**ITextRangeProvider**](uiauto-itextrangeprovider.md) interface provides several methods for manipulating and navigating text ranges in a text-based control. The [**ITextRangeProvider::Move**](uiauto-itextrangeprovider-move.md), [**MoveEndpointByUnit**](uiauto-itextrangeprovider-moveendpointbyunit.md), and [**ExpandToEnclosingUnit**](uiauto-itextrangeprovider-expandtoenclosingunit.md) methods move a text range or one of its endpoints by the specified text unit, such as character, word, paragraph, and so on. For more information, see [UI Automation Text Units](https://msdn.microsoft.com/library/windows/desktop/ff384860).

Despite its name, the [**ITextRangeProvider::ExpandToEnclosingUnit**](uiauto-itextrangeprovider-expandtoenclosingunit.md) method does not necessarily expand a text range. Instead, it "normalizes" a text range by moving the endpoints so that the range encompasses the specified text unit. The range is expanded if it is smaller than the specified unit, or shortened if it is longer than the specified unit. It is critical that the **ExpandToEnclosingUnit** method always normalizes text ranges in a consistent manner; otherwise, other aspects of text range manipulation by text unit would be unpredictable. The following diagram shows how **ExpandToEnclosingUnit** normalizes a text range by moving the endpoints of the range.

![diagram showing text range endpoint positions before and after a call to expandtoenclosingunit](images/expandtoenclosingunit.jpg)

If the text range starts at the beginning of a text unit and ends at the beginning of, or before, the next text unit boundary, the ending endpoint is moved to the next text unit boundary (see 1 and 2 in the previous diagram).

If the text range starts at the beginning of a text unit and ends at, or after, the next unit boundary, the ending endpoint stays or is moved backward to the next unit boundary after the starting endpoint (see 3 and 4 in the previous illustration). If there is more than one text unit boundary between the starting and ending endpoints, the ending endpoint is moved backward to the next unit boundary after the starting endpoint, resulting in a text range that is one text unit in length.

If the text range starts in a middle of the text unit, the starting endpoint is moved backward to the beginning of the text unit, and the ending endpoint is moved forward or backward, as necessary, to the next unit boundary after the starting endpoint (see 5 through 8 in the previous diagram).

When the [**ITextRangeProvider::Move**](uiauto-itextrangeprovider-move.md) method is called, the provider normalizes the text range by the specified text unit, using the same normalization logic as the [**ExpandToEnclosingUnit**](uiauto-itextrangeprovider-expandtoenclosingunit.md) method. Then, the provider moves the range backward or forward by the specified number of text units. When moving the range, the provider should ignore the boundaries of any embedded objects in the text. (However, the unit boundary itself may be affected by the existence of an embedded object). The following diagram demonstrates how the **Move** method moves a text range, unit by unit, across embedded objects and text unit boundaries.

![diagram showing how the move method moves range endpoints across object and text unit boundaries](images/move.jpg)

The [**ITextRangeProvider::MoveEndpointByUnit**](uiauto-itextrangeprovider-moveendpointbyunit.md) method moves one of the endpoints forward or backward by specified text unit, as the following illustration shows.

![diagram showing how moveendpointbyunit moves the endpoint of a range](images/moveendpointbyunit.gif)

The [**ITextRangeProvider::MoveEndpointByRange**](uiauto-itextrangeprovider-moveendpointbyrange.md) method enables a client application to set one endpoint of a text range to same location as the specified endpoint of a second text range.

### Selecting Text in a Text Range

The [**ITextRangeProvider**](uiauto-itextrangeprovider.md) interface includes several methods for controlling the selection of text in a text-based control.

The [**ITextRangeProvider::Select**](uiauto-itextrangeprovider-select.md) method should select the text that corresponds to a text range, and remove the previous selection, if any, from the text control. If **Select** is called on a degenerate text range, the provider should move the insertion point to the location of the text range without selecting any text.

If a control supports the selection of multiple, disjoint spans of text, the [**ITextRangeProvider::AddToSelection**](uiauto-itextrangeprovider-addtoselection.md) and [**RemoveFromSelection**](uiauto-itextrangeprovider-removefromselection.md) methods add text ranges to, and remove them from, the collection of selected text ranges. If the control only supports one selected text range at a time, but the selection operation would result in the selection of multiple disjoint text ranges, the provider should either return an **E\_INVALIDOPERATION** error, or should extend or truncate the current selection. The [**ITextProvider::SupportedTextSelection**](uiauto-itextprovider-supportedtextselection.md) property should indicate whether a control supports the selection of single or multiple spans of text, or none at all.

If a text-based control supports text insertions, calling [**ITextRangeProvider::AddToSelection**](uiauto-itextrangeprovider-addtoselection.md) or [**RemoveFromSelection**](uiauto-itextrangeprovider-removefromselection.md) on a degenerate text range in the control should move the insertion point but should not select any text.

### Acquiring Text from a Text Range

The [**ITextRangeProvider::GetText**](uiauto-itextrangeprovider-gettext.md) method should return the plain text of a text range. The plain text should include all control characters found in the source text, such as carriage returns and the Unicode left-to-right mark (LRM). The plain text should not include any markup tags such as HTML that may be present in the source text. Also, any escape codes in the source text should be converted to the plain text equivalents. For example, "&nbsp;" should be converted to a simple space character.

If an embedded object spans a range of text, the plain text should include the inner text of the object, but not the alternative text (the name property of the embedded object) because it might be inconsistent with the descriptive inner text. For more information, see [How UI Automation Exposes Embedded Objects](uiauto-textpattern-and-embedded-objects-overview.md).

### Implementing ShowContextMenu

[**ITextRangeProvider2::ShowContextMenu**](uiauto-itextrangeprovider2-showcontextmenu.md) should always show the context menu at the beginning end point of the range. This should be equivalent to what would happen if the user pressed the context menu key or SHIFT + F10 with the insertion point at the beginning of the range.

If showing the context menu would typically result in the insertion point moving to a given location, then it should do so for programmatically invoking [**ShowContextMenu**](uiauto-itextrangeprovider2-showcontextmenu.md) for UI Automation support also.

## Interoperability with the System Caret

Correctly supporting the insertion point is critical to many client applications, including those not based on UI Automation. In the **Text** control pattern, the insertion point is represented by a degenerate (empty) text range at the position of the system caret. When the insertion point moves, a control should raise the **TextSelectionChanged** event ([**UIA\_Text\_TextSelectionChangedEventId**](uiauto-event-ids.md#uia-text-textselectionchangedeventid)). Some client applications depend on this event to monitor the location of the insertion point, and to keep track of the text selection.

When a control contains selected text, the current design of the **Text** control pattern does not provide a way to directly associate the location of the insertion point with a particular text range. The provider must keep track of the text selection and set the location of the insertion point appropriately. Because selecting text is typically done by moving the insertion point while holding down the SHIFT or CTRL key, or both, a provider can track the text selection by checking the state of these keys whenever the selection changes.

Because the accessibility framework provides built-in support for the system caret but not for a custom caret, text-based controls should use the system caret whenever possible. Controls that use a custom caret can ensure that the caret is accessible by creating a system caret that has the same dimensions as the custom caret, and positioning the system caret at the same location in the UI of the control as the custom caret—that is, at the insertion point. As an alternative, a control that uses a custom caret can implement a Microsoft Active Accessibility provider for [**OBJID\_CARET**](object-identifiers.md#objid-caret) to provide accessibility information directly for your custom caret.

For more information about the system caret, see [Carets](https://msdn.microsoft.com/library/windows/desktop/ms646968).

To test whether your control properly exposes the location of the system caret, use the [Inspect](inspect-objects.md) and [Accessible Event Watcher](accessible-event-watcher.md) tools.

The screen coordinates of the center of the system caret bitmap should always match the location of the insertion point. That way, a client can use the caret screen coordinates in a call to [**ITextProvider::RangeFromPoint**](uiauto-itextprovider-rangefrompoint.md) to retrieve a text range that accurately represents the location of the insertion point.

## Related topics

<dl> <dt>

[Control Types and Their Supported Control Patterns](uiauto-controlpatternmapping.md)
</dt> <dt>

[Text Control Type](uiauto-supporttextcontroltype.md)
</dt> <dt>

[TextEdit Control Pattern](textedit-control-pattern.md)
</dt> <dt>

[TextChild Control Pattern](textchild-control-pattern.md)
</dt> <dt>

[UI Automation Control Patterns Overview](uiauto-controlpatternsoverview.md)
</dt> <dt>

[UI Automation Support for Textual Content](uiauto-ui-automation-textpattern-overview.md)
</dt> <dt>

[UI Automation Tree Overview](uiauto-treeoverview.md)
</dt> </dl>

 

 



