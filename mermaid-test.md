hello, this is a mermaid test

---

this is a raw mermaid code block:

```mermaid
flowchart TD
     a-->b
```

<script src="https://cdnjs.cloudflare.com/ajax/libs/mermaid/10.1.0/mermaid.min.js"></script>
<script>
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
