---
title: Recipe Generator
slug: /R&D/recipe-generator
---

AI based agroforestry recipe generator built on top of a knowledge graph database. 

### Inserting Data 
```javascript
// insert genus
function taxonomicGenus(item) {
    return `insert $g isa taxonomic-genus, has name "${item.genus}";`;
}
//insert species
function taxonomicSpecies(item) {
    return `insert $s isa taxonomic-species, has name "${item.species}";`;
}
//insert taxonomic relationship Genus and Species
function taxonomicHierarchyGenusSpecies(item) {
    const { genus, species } = item;
    // match genus
    let graqlInsertQuery = `match $g isa taxonomic-genus, has name "${genus}"; `;
    // match species
    graqlInsertQuery += `$s isa taxonomic-species, has name "${species}"; `;
    // insert taxonomic-hierarchy
    graqlInsertQuery +=
        "insert (super-taxon: $g, sub-taxon: $s) isa taxonomic-hierarchy;";
    return graqlInsertQuery;
}
```
### Knowledge Graph Deductive Reasoning
```javascript
koppen-Af sub rule,
when{
    $loc isa location, has precipitation-annual $pannual, has precipitation-thresholdx10 $pthold10,
    has temperature-min $tmin, has precipitation-dry $pdry;
    $kop isa koppen, has classification "Af";
    # A
    $tmin >= 18;
    $pannual >= $pthold10;
    # f
    $pdry >= 60;
}, then{
    (biome-of: $kop, biome-belongs:$loc) isa koppen-geiger;
};
```


### Querying Data 
```javascript
match 
 
$kg($loc,$kop) isa koppen-geiger; $loc isa location; $kop isa koppen;
$kg2($sp,$kop) isa koppen-geiger; $sp isa taxonomic-species;  
 
get;
offset 0; limit 7;
```