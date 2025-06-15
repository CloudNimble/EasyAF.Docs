# Search Implementation

## Overview

Search is a critical component of the SimpleMessageBus documentation site, enabling users to quickly find API references, guides, and examples. This document outlines the search implementation strategy using Algolia DocSearch with fallback options.

## Primary Strategy: Algolia DocSearch

### Why Algolia?

Algolia DocSearch is the gold standard for documentation search, offering:
- Sub-100ms search responses
- Typo tolerance and synonyms
- Contextual results ranking
- Beautiful UI components
- Free for open source projects
- Used by major documentation sites

### Implementation Approach

```javascript
// docusaurus.config.js
module.exports = {
  themeConfig: {
    algolia: {
      appId: 'YOUR_APP_ID',
      apiKey: 'YOUR_API_KEY',
      indexName: 'simplemessagebus',
      contextualSearch: true,
      searchParameters: {
        facetFilters: ['version:VERSION', 'language:LANGUAGE']
      },
      searchPagePath: 'search',
    },
  },
};
```

### Index Configuration

```json
{
  "index_name": "simplemessagebus",
  "start_urls": [
    {
      "url": "https://docs.simplemessagebus.com/api/",
      "selectors_key": "api",
      "tags": ["api-reference"]
    },
    {
      "url": "https://docs.simplemessagebus.com/docs/",
      "selectors_key": "docs",
      "tags": ["guides"]
    }
  ],
  "selectors": {
    "api": {
      "lvl0": {
        "selector": ".api-header h1",
        "global": true
      },
      "lvl1": ".api-content h2",
      "lvl2": ".api-content h3",
      "lvl3": ".api-content h4",
      "text": ".api-content p, .api-content li, .api-content code"
    },
    "docs": {
      "lvl0": {
        "selector": "(//ul[contains(@class,'menu__list')]//a[contains(@class, 'menu__link menu__link--sublist menu__link--active')]/text() | //nav[contains(@class, 'navbar')]//a[contains(@class, 'navbar__link--active')]/text())[last()]",
        "global": true
      },
      "lvl1": "header h1",
      "lvl2": "article h2",
      "lvl3": "article h3",
      "text": "article p, article li"
    }
  },
  "custom_settings": {
    "attributesForFaceting": [
      "language",
      "version",
      "type",
      "docusaurus_tag"
    ],
    "attributesToRetrieve": [
      "hierarchy",
      "content",
      "anchor",
      "url",
      "url_without_anchor",
      "type"
    ],
    "attributesToHighlight": ["hierarchy", "content"],
    "attributesToSnippet": ["content:10"],
    "camelCaseAttributes": ["hierarchy", "content"],
    "searchableAttributes": [
      "unordered(hierarchy.lvl0)",
      "unordered(hierarchy.lvl1)",
      "unordered(hierarchy.lvl2)",
      "unordered(hierarchy.lvl3)",
      "content"
    ],
    "distinct": true,
    "attributeForDistinct": "url",
    "customRanking": [
      "desc(weight.pageRank)",
      "desc(weight.level)",
      "asc(weight.position)"
    ],
    "ranking": [
      "words",
      "filters",
      "typo",
      "attribute",
      "proximity",
      "exact",
      "custom"
    ],
    "highlightPreTag": "<mark>",
    "highlightPostTag": "</mark>",
    "minWordSizefor1Typo": 3,
    "minWordSizefor2Typos": 7,
    "allowTyposOnNumericTokens": false,
    "minProximity": 1,
    "ignorePlurals": true,
    "advancedSyntax": true,
    "attributeCriteriaComputedByMinProximity": true,
    "removeWordsIfNoResults": "allOptional",
    "separatorsToIndex": "_",
    "synonyms": [
      ["message", "msg"],
      ["publisher", "sender"],
      ["handler", "processor", "consumer"],
      ["queue", "channel"],
      ["azure", "asq", "azure storage queue"],
      ["amazon", "aws", "sqs"],
      ["filesystem", "file system", "fs"]
    ]
  }
}
```

## Search UI Components

### 1. Search Bar Component

```jsx
// src/components/SearchBar/index.js
import React from 'react';
import { DocSearch } from '@docsearch/react';
import '@docsearch/css';

export default function SearchBar() {
  return (
    <DocSearch
      appId={process.env.ALGOLIA_APP_ID}
      indexName="simplemessagebus"
      apiKey={process.env.ALGOLIA_API_KEY}
      placeholder="Search documentation..."
      translations={{
        button: {
          buttonText: 'Search',
          buttonAriaLabel: 'Search',
        },
        modal: {
          searchBox: {
            resetButtonTitle: 'Clear query',
            resetButtonAriaLabel: 'Clear query',
            cancelButtonText: 'Cancel',
            cancelButtonAriaLabel: 'Cancel',
          },
          footer: {
            selectText: 'to select',
            selectKeyAriaLabel: 'Enter key',
            navigateText: 'to navigate',
            navigateUpKeyAriaLabel: 'Arrow up',
            navigateDownKeyAriaLabel: 'Arrow down',
            closeText: 'to close',
            closeKeyAriaLabel: 'Escape key',
            searchByText: 'Search by',
          },
          noResultsScreen: {
            noResultsText: 'No results for',
            suggestedQueryText: 'Try searching for',
            reportMissingResultsText: 'Believe this query should return results?',
            reportMissingResultsLinkText: 'Let us know.',
          },
        },
      }}
      transformItems={(items) => {
        return items.map((item) => ({
          ...item,
          // Add type badges
          badge: item.type === 'api' ? 'API' : 
                 item.type === 'guide' ? 'Guide' : 
                 item.type === 'sample' ? 'Sample' : null,
        }));
      }}
      getMissingResultsUrl={({ query }) => {
        return `https://github.com/CloudNimble/SimpleMessageBus/issues/new?title=Missing+search+results+for+${query}`;
      }}
    />
  );
}
```

### 2. Search Results Page

```jsx
// src/pages/search.js
import React from 'react';
import Layout from '@theme/Layout';
import { InstantSearch, SearchBox, Hits, RefinementList, Pagination } from 'react-instantsearch-dom';
import algoliasearch from 'algoliasearch/lite';

const searchClient = algoliasearch(
  process.env.ALGOLIA_APP_ID,
  process.env.ALGOLIA_API_KEY
);

function Hit({ hit }) {
  return (
    <article className="search-result">
      <h3>
        <a href={hit.url}>
          {hit.hierarchy.lvl0 && <span className="breadcrumb">{hit.hierarchy.lvl0} › </span>}
          {hit.hierarchy.lvl1 || hit.hierarchy.lvl0}
        </a>
      </h3>
      <p dangerouslySetInnerHTML={{ __html: hit._snippetResult.content.value }} />
      <div className="search-meta">
        {hit.type && <span className={`badge badge--${hit.type}`}>{hit.type}</span>}
        {hit.version && <span className="version">v{hit.version}</span>}
      </div>
    </article>
  );
}

export default function SearchPage() {
  return (
    <Layout title="Search">
      <div className="container margin-vert--lg">
        <InstantSearch searchClient={searchClient} indexName="simplemessagebus">
          <div className="row">
            <div className="col col--3">
              <h3>Filter by Type</h3>
              <RefinementList attribute="type" />
              <h3>Filter by Version</h3>
              <RefinementList attribute="version" />
            </div>
            <div className="col col--9">
              <SearchBox 
                autoFocus
                searchAsYouType={true}
                showLoadingIndicator
              />
              <Hits hitComponent={Hit} />
              <Pagination />
            </div>
          </div>
        </InstantSearch>
      </div>
    </Layout>
  );
}
```

## Fallback Strategy: Local Search

For environments where Algolia isn't available, implement local search:

```javascript
// docusaurus.config.js
module.exports = {
  themes: [
    [
      require.resolve("@easyops-cn/docusaurus-search-local"),
      {
        hashed: true,
        language: ["en"],
        highlightSearchTermsOnTargetPage: true,
        explicitSearchResultPath: true,
        indexBlog: false,
        docsRouteBasePath: "/docs",
        searchResultLimits: 10,
        searchResultContextMaxLength: 50,
      },
    ],
  ],
};
```

## Search Enhancement Features

### 1. Smart Suggestions

```javascript
// Implement smart suggestions based on common queries
const commonQueries = [
  { query: "getting started", url: "/docs/getting-started" },
  { query: "publish message", url: "/docs/guides/publishing" },
  { query: "azure setup", url: "/docs/providers/azure/setup" },
  { query: "error handling", url: "/docs/concepts/error-handling" },
];

// Add to search configuration
transformItems: (items) => {
  if (items.length === 0) {
    return commonQueries.filter(q => 
      q.query.includes(searchQuery.toLowerCase())
    );
  }
  return items;
}
```

### 2. Search Analytics

```javascript
// Track search queries for improvement
window.gtag('event', 'search', {
  search_term: query,
  results_count: results.length,
  clicked_result: clickedItem?.url
});

// Monthly analysis of:
// - Top search queries
// - No-result queries
// - Click-through rates
// - Search refinements
```

### 3. Contextual Search

```javascript
// Boost results based on current page context
searchParameters: {
  // Boost API results when on API pages
  optionalFilters: currentPath.includes('/api/') ? 
    ['type:api<score=2>'] : [],
  // Boost provider-specific results
  facetFilters: currentProvider ? 
    [`provider:${currentProvider}`] : []
}
```

## Search Indexing Pipeline

### Build-Time Indexing

```javascript
// scripts/build-search-index.js
const { DocSearchIndexer } = require('./doc-search-indexer');

async function buildSearchIndex() {
  const indexer = new DocSearchIndexer({
    siteUrl: 'https://docs.simplemessagebus.com',
    outputPath: './search-index.json',
  });
  
  // Index all content
  await indexer.indexDocs('./docs/**/*.md');
  await indexer.indexApi('./api/**/*.mdx');
  
  // Generate metadata
  await indexer.generateSitemap();
  await indexer.generateSearchMeta();
  
  // Upload to Algolia
  if (process.env.CI) {
    await indexer.uploadToAlgolia();
  }
}
```

### API Documentation Indexing

```javascript
// Special handling for API docs
function indexApiDoc(doc) {
  return {
    objectID: doc.uid,
    url: `/api/${doc.namespace}/${doc.name}`,
    hierarchy: {
      lvl0: doc.namespace,
      lvl1: doc.name,
      lvl2: doc.memberType,
    },
    content: doc.summary,
    type: 'api',
    memberType: doc.memberType,
    syntax: doc.syntax,
    parameters: doc.parameters?.map(p => p.name),
    returnType: doc.returns?.type,
    // Boost important API members
    weight: {
      pageRank: doc.isPublic ? 100 : 50,
      level: doc.memberType === 'Constructor' ? 90 : 70,
    }
  };
}
```

## Performance Optimization

### 1. Lazy Loading

```javascript
// Lazy load search components
const SearchBar = React.lazy(() => 
  import('@site/src/components/SearchBar')
);

// Preload on hover
<div 
  onMouseEnter={() => {
    import('@site/src/components/SearchBar');
  }}
>
  <React.Suspense fallback={<div>Search...</div>}>
    <SearchBar />
  </React.Suspense>
</div>
```

### 2. Caching Strategy

```javascript
// Cache search results
const searchCache = new Map();
const CACHE_TTL = 5 * 60 * 1000; // 5 minutes

async function cachedSearch(query) {
  const cached = searchCache.get(query);
  if (cached && Date.now() - cached.timestamp < CACHE_TTL) {
    return cached.results;
  }
  
  const results = await performSearch(query);
  searchCache.set(query, {
    results,
    timestamp: Date.now()
  });
  
  return results;
}
```

### 3. Debouncing

```javascript
// Debounce search input
const debouncedSearch = useMemo(
  () => debounce(handleSearch, 300),
  []
);
```

## Accessibility

### Keyboard Navigation

```javascript
// Full keyboard support
const searchShortcuts = {
  '/': 'Focus search',
  'Escape': 'Close search',
  'ArrowDown': 'Next result',
  'ArrowUp': 'Previous result',
  'Enter': 'Select result',
  'Cmd+K': 'Open search modal'
};
```

### Screen Reader Support

```html
<div role="search" aria-label="Search documentation">
  <input
    type="search"
    aria-label="Search documentation"
    aria-describedby="search-instructions"
    aria-autocomplete="list"
    aria-controls="search-results"
  />
  <div id="search-instructions" className="sr-only">
    Type to search. Use arrow keys to navigate results.
  </div>
  <div 
    id="search-results" 
    role="listbox"
    aria-label="Search results"
  >
    <!-- Results -->
  </div>
</div>
```

## Search Quality Monitoring

### Metrics to Track

1. **Search Performance**
   - Query response time
   - Result relevance scores
   - Zero-result rate

2. **User Behavior**
   - Click-through rate
   - Result position clicked
   - Query refinements
   - Session duration after search

3. **Content Gaps**
   - Frequently searched missing content
   - Low-relevance queries
   - Common typos/synonyms

### Implementation

```javascript
// Analytics integration
function trackSearch(event) {
  analytics.track('Documentation Search', {
    query: event.query,
    resultsCount: event.results.length,
    responseTime: event.responseTime,
    clickedPosition: event.clickedPosition,
    clickedUrl: event.clickedUrl,
    refinements: event.refinements
  });
}
```

## Future Enhancements

1. **AI-Powered Search**
   - Natural language queries
   - Semantic search
   - Query understanding

2. **Personalization**
   - Search history
   - Recommended content
   - Role-based results

3. **Advanced Features**
   - Code search within examples
   - Visual search for diagrams
   - Voice search