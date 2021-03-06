
# Imbrium 
_A Lunr.js Extension to Support Large Scale Search on Any Scale Devices_


The [https://github.com/olivernn/lunr.js](Lunr.js in-memory search engine) is very fast and flexible but cannot be used in mobile apps for large data indexing because Lunr's entire index must be loaded into memory.  

This can probably be overcome by splitting the search index into small pieces but that again is only possible if the index bits can be loaded very quickly. At present loading a 6mb sample takes 7-9 seconds on a laptop. Our goal should be 2-3 mb index hunks loaded in under 20ms on a mobile device.  

## Key Features

### 1. Replace Trie

- Replace inefficient trie with compact, fast-loading succinct trie
- On the large trie: [https://github.com/olivernn/lunr.js/issues/85](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Folivernn%2Flunr.js%2Fissues%2F85&sa=D&sntz=1&usg=AFQjCNH6KAB5cuBNRcAFGs55AIXB9MYcfw)
    - Code: [https://github.com/olivernn/lunr.js/blob/next/lib/trie.js](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Folivernn%2Flunr.js%2Fblob%2Fnext%2Flib%2Ftrie.js&sa=D&sntz=1&usg=AFQjCNF6FQLTfVEL_OKkJa7OacOhIRw0dw) 
- Succinct trie: [http://stevehanov.ca/blog/index.php?id=120](http://www.google.com/url?q=http%3A%2F%2Fstevehanov.ca%2Fblog%2Findex.php%3Fid%3D120&sa=D&sntz=1&usg=AFQjCNGhMaSpLujbJ7fsmywveHSydHNwAg) 
    - Code: [http://www.hanovsolutions.com/trie/Bits.js](http://www.google.com/url?q=http%3A%2F%2Fwww.hanovsolutions.com%2Ftrie%2FBits.js&sa=D&sntz=1&usg=AFQjCNEOJ1Sppt29AuXn1Pj1qoRqYDPAtQ)

### 2. Search Objects 

- Override Lunr.js search to accept detailed search object
- Optional function for fetching text sections/documents by id 
  - Same function and id can then be used for secondary search/display
- Optional function for determining (or even providing) trie index to use for each word
- Optional function for providing secondary search analysis on each hit 
- Provide split up search phrase supporting more advanced syntax such as:
    - exclude flag __-word__
    - metadata search __(authorname, booktitle, subject)__
    - exact phrase matching __"find this exactly"__

### 3. Index Sharding 

- Add Lunr.js interface for sharding dictionary by first letter or first two letters
    - Add interface for merging a full index with multiple sharded indexes
    - Add interface for deleting document from sharded indexes 
    
### 4. Secondary Search Analysis 

- Advanced secondary search quality scoring
- Use registered function for fetching text block from id
  - Should work with pext or HTML
  - Receives search object with full, stemmed and metaphone data
- Locate matching terms in text block (by metaphone if used)
  - Return terms, position, metaphone, length etc. (all information needed for markup and display)
- Compare stemmed terms for similarity (reject false positives)
  - http://en.wikipedia.org/wiki/Levenshtein_distance
  - [https://github.com/gf3/Levenshtein](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Fgf3%2FLevenshtein&sa=D&sntz=1&usg=AFQjCNGDmmAl0vpEzsN3TGkz0cGftuxJ-g)
- Semantic proximity score boost
  - Boost score if terms are in adjacent sentence, more if same sentence.
  - Boost score if terms are in adjacent phrase, more if same phrase
  - Boost score if terms are in the search phrase sequence (disregarding stop words)
  - Boost score for each term matching exactly (with stemming) more for terms matching without stemming
- Narrow results if many returned
  - Remove hits with very low secondary scores
  - Boost score of hits based on optional metadata multiplier option (such as author authority)
  - If too many results are obtained, increase cutoff threshold until fewer results are returned (specific numbers flexible)
  - Order documents according to score total of remaining doc hits (since hits will remain clustered by document regardless of scores)

### 5. Full Internationalization 

- Update Metaphone filter to use Dual Metaphone or Metaphone 3
  - [http://en.wikipedia.org/wiki/Metaphone](http://www.google.com/url?q=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMetaphone&sa=D&sntz=1&usg=AFQjCNFy3GgAFEli4L3gBo212EyChKkI3w)
  - [http://www.amorphics.com/metaphone3.html](http://www.google.com/url?q=http%3A%2F%2Fwww.amorphics.com%2Fmetaphone3.html&sa=D&sntz=1&usg=AFQjCNHVhet8-JDAUfEMO5Xec9dwQU92Ow) (metaphone'sauthor, Lawrence Philips, has offered to provide JS code)
- Launch open project to create Metaphone implementations in all major languages. Sponsor development.


-------------------------------------------------------

# Development Plan

* Create test page to load and parse books into local storage (both disk and IndexDB)
  * [http://chadananda.github.io/Imbrium/](http://chadananda.github.io/Imbrium/) 
  * Pull down Gutenberg books, strip out header and footer, split by paragraph
  * Spider pages from Wikipedia (slowly), split by blocks 
  * For file storage, store manifest document and block documents (by base 36 id) as docid-blockid
* Provide indexing and search for both Lunr.js and Imbrium.js, log time and memory usage
* Implement trie replacement 
  * Create pull request for Lunr.js
* Implement search object with 
  * hooks for index lookup, textblock lookup, secondary analysis
  * language selection
* Implement dual-metaphone
  * Create pull request for dual-metaphone   
* Implement textblock lookup
* Implement secondary analysis
* Implement index sharding by first two letters 

 
    



