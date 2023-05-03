hello, this is a mermaid test

---

this is a raw mermaid code block:

```mermaid
flowchart TD
     a-->b
```

<script type="module">
import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
var config = {
    startOnLoad:true,
    theme: 'dark',
    flowchart:{
            useMaxWidth:false,
            htmlLabels:true
        }
};
mermaid.initialize(config);
window.mermaid.init(undefined, document.querySelectorAll('.language-mermaid'));
</script>
