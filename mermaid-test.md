hello, this is a mermaid test

---

this is a raw mermaid code block:

```mermaid
flowchart TD
     a-->b
```

<script src="https://cdn.jsdelivr.net/npm/mermaid@10.1.0/dist/arc-f7872e1e.min.js"></script>
<script>
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
