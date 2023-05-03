---
mermaid: true
---

hello, this is a mermaid test

---

this is a raw mermaid code block:

```mermaid
flowchart TD
     a-->b
```
<script src="https://cdn.jsdelivr.net/npm/mermaid@10.0.2/dist/svgDraw-c034b55e.min.js"></script>
<script>
  $(document).ready(function () {
    mermaid.initialize({
      startOnLoad:true,
      theme: "default",
    });
    window.mermaid.init(undefined, document.querySelectorAll('.language-mermaid'));
  });
</script>
