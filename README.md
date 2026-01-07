# NT2 Negative Firefox

A demonstration repository for reproducing Firefox bug [2008331](https://bugzilla.mozilla.org/show_bug.cgi?id=2008331): PerformanceNavigationTiming returns -1 timestamps for memory-cache navigations.

## Bug Summary

Navigation Timing Level 2 returns `-1` for timing attributes when documents are loaded from memory cache in Firefox.

**Affected Browser:** Firefox 146.0.1 (x64)
**Affected OS:** Windows 11 24H2 OS Build 26100.7462

## Problem Description

When a document is served from memory cache (`transferSize === 0`), Firefox's PerformanceNavigationTiming sometimes returns `-1` for timing attributes such as:

- `fetchStart`
- `requestStart`
- `responseStart`
- `responseEnd`

Example output showing the issue:
```json
{
  "label": "load",
  "time": "2026-01-02T07:03:32.680Z",
  "navType": "navigate",
  "responseStart": -1,
  "fetchStart": -1,
  "requestStart": -1,
  "responseEnd": -1,
  "domContentLoadedEventEnd": 19,
  "loadEventEnd": 0,
  "transferSize": 0,
  "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:148.0) Gecko/20100101 Firefox/148.0"
}
```

## Expected Behavior

According to the [Navigation Timing Level 2 specification](https://www.w3.org/TR/navigation-timing-2/), these timing attributes should return valid DOMHighResTimeStamp values (>= 0), even when resources are served from memory cache. The specification does not define `-1` as a valid sentinel value for these attributes.

## How to Reproduce

1. Open Firefox in private mode to minimize cache and extension effects
2. Navigate to: https://mimori256.github.io/nt2-negative-firefox/
3. Check the "Result" output and confirm `transferSize` is positive (document not loaded from memory cache)
4. Click the address bar and press Enter to reload the same URL (navigation type should remain "navigate")
5. Check the "Result" output again and confirm `transferSize` is 0 (document loaded from memory cache)
6. Observe the values of `responseStart`, `fetchStart`, `requestStart`, and `responseEnd` - they may show `-1`

## Repository Structure

- `index.html` - English version of the demonstration page
- `index-ja.html` - Japanese version of the demonstration page

## Technical Details

The demonstration uses JavaScript to capture PerformanceNavigationTiming data on page load:

```javascript
const nav = performance.getEntriesByType('navigation')[0];
// Captures timing data including fetchStart, requestStart, responseStart, responseEnd, etc.
```

## Related Issues

### WebKit Bug
A very similar issue exists in WebKit: ["Navigation Timing L2 often returns negative timing values for documents served from memory cache"](https://bugs.webkit.org/show_bug.cgi?id=258014)

### RUM Community Group Discussion
Related discussion: https://github.com/w3c-cg/rum/issues/1

## Contributing

This repository serves as a demonstration for the bug report. If you encounter this issue or have additional information, please contribute to the Bugzilla report.
