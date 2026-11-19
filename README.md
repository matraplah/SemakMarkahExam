# SemakMarkahExam

A score-checking page for Sains 2026, hosted on Netlify. Students enter their IC number and see their marks.

Marks come from the Google Apps Script web app set in `config.js` (`window.FIZIK_GAS_URL`).
If no URL is set, the page runs in demo mode with sample data.

## Apps Script setup (required)

The page calls the web app as `…/exec?ic=<IC>&callback=<name>` (JSONP).
In the Apps Script project, replace `doGet` with this (keep your existing `semakMarkah(ic)` function):

```js
function doGet(e) {
  var p = (e && e.parameter) || {};
  if (p.ic) {
    var res;
    try { res = semakMarkah(p.ic); }
    catch (err) { res = { ok: false, mesej: 'Ralat pelayan. Cuba lagi.' }; }
    var cb = String(p.callback || '').replace(/[^\w$]/g, '');
    var json = JSON.stringify(res);
    return cb
      ? ContentService.createTextOutput(cb + '(' + json + ');').setMimeType(ContentService.MimeType.JAVASCRIPT)
      : ContentService.createTextOutput(json).setMimeType(ContentService.MimeType.JSON);
  }
  // No IC: serve the old HTML page, as before
  return HtmlService.createHtmlOutputFromFile('index');
}
```

Then go to **Deploy → Manage deployments → Edit → New version → Deploy**. The URL stays the same.
The deployment must be set to **Execute as: Me** and **Who has access: Anyone**.
