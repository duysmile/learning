## Some common regex:

- Convert number string for greater readability:
Eg: 1000000 -> 1,000,000
`(\d)(?=(\d{3})+\b)` -> replace with `$1,`
