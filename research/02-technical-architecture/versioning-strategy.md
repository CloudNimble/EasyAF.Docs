# Versioning Strategy

## Overview

Managing multiple versions of SimpleMessageBus documentation is crucial for supporting users across different releases while maintaining a sustainable maintenance burden. This document outlines the versioning strategy for the documentation site.

## Versioning Approach

### Version Support Policy

**Active Support:**
- **Current Version (v6.x)**: Full documentation, active updates
- **Previous Version (v5.x)**: Security updates only, archived docs
- **Legacy Versions (v4.x and older)**: Read-only archive

**Deprecation Timeline:**
- New major version: Previous becomes maintenance-only
- After 12 months: Move to archive-only
- After 24 months: Consider removal (with migration guide)

### Documentation Version Strategy

```
docs.simplemessagebus.com/
├── /                     # Latest version (v6.x)
├── /6.0/                 # Specific v6.0 docs
├── /5.0/                 # Archived v5.0 docs
├── /4.0/                 # Legacy v4.0 docs (read-only)
└── /next/                # Unreleased development docs
```

## Docusaurus Versioning Implementation

### Configuration

```javascript
// docusaurus.config.js
module.exports = {
  presets: [
    [
      'classic',
      {
        docs: {
          sidebarPath: require.resolve('./sidebars.js'),
          // Enable versioning
          includeCurrentVersion: true,
          lastVersion: '6.0',
          versions: {
            current: {
              label: '6.0 (Latest)',
              path: '/',
              badge: false,
            },
            '5.0': {
              label: '5.0',
              path: '/5.0/',
              badge: true,
              banner: 'unmaintained',
            },
            '4.0': {
              label: '4.0 (Legacy)',
              path: '/4.0/',
              badge: true,
              banner: 'unmaintained',
            },
          },
          onlyIncludeVersions: ['current', '5.0', '4.0'],
        },
      },
    ],
  ],
};
```

### Version Creation Workflow

```bash
# Create new version when releasing
npm run docusaurus docs:version 6.0

# This creates:
# - versioned_docs/version-6.0/
# - versioned_sidebars/version-6.0-sidebars.json
# - versions.json (updated)
```

## Content Versioning Strategy

### 1. API Documentation Versioning

Each version maintains its own API documentation:

```
api/
├── current/              # v6.x API docs
│   ├── core/
│   ├── publishing/
│   └── dispatching/
├── 5.0/                  # v5.x API docs (archived)
│   ├── core/
│   └── publishing/
└── version-manifest.json # Version metadata
```

### 2. Version-Specific Build Pipeline

```javascript
// scripts/build-versioned-docs.js
async function buildVersionedDocs() {
  const versions = ['current', '5.0', '4.0'];
  
  for (const version of versions) {
    console.log(`Building docs for version ${version}`);
    
    // Build API docs for specific version
    await buildApiDocs(version);
    
    // Process version-specific content
    await processVersionContent(version);
    
    // Update navigation
    await updateVersionNavigation(version);
  }
}

async function buildApiDocs(version) {
  const config = {
    version,
    sourcePath: `./src-${version}/`,
    outputPath: `./api/${version}/`,
    xmlDocPath: `./xml-docs/${version}/`,
  };
  
  // Generate API docs for specific version
  await generateApiReference(config);
}
```

### 3. Cross-Version Linking

```jsx
// src/components/VersionLink/index.js
export default function VersionLink({ to, version, children }) {
  const currentVersion = useDocusaurusContext().siteConfig.customFields.version;
  
  // Smart version-aware linking
  const versionPath = version === 'current' ? '' : `/${version}`;
  const fullPath = `${versionPath}${to}`;
  
  return (
    <Link to={fullPath} className="version-link">
      {children}
      {version !== currentVersion && (
        <span className="version-badge">{version}</span>
      )}
    </Link>
  );
}
```

## Migration Management

### Version Migration Guides

```markdown
# Migrating from v5.0 to v6.0

## Breaking Changes

### 1. IMessage Interface Changes
- **v5.0**: `IMessage.Id` was `string`
- **v6.0**: `IMessage.Id` is now `Guid`

**Migration:**
```csharp
// Before (v5.0)
public class OrderMessage : IMessage
{
    public string Id { get; set; } = Guid.NewGuid().ToString();
}

// After (v6.0)
public class OrderMessage : IMessage
{
    public Guid Id { get; set; } = Guid.NewGuid();
}
```

### 2. Publisher Registration Changes
- **v5.0**: Manual registration required
- **v6.0**: Auto-discovery enabled

**Migration:**
```csharp
// Before (v5.0)
services.AddSingleton<IMessagePublisher, FileSystemMessagePublisher>();

// After (v6.0)
services.AddSimpleMessageBus()
    .UseFileSystemProvider(options => { /* config */ });
```
```

### Automated Migration Tools

```javascript
// scripts/generate-migration-guide.js
async function generateMigrationGuide(fromVersion, toVersion) {
  const changes = await analyzeBreakingChanges(fromVersion, toVersion);
  
  const guide = {
    title: `Migrating from ${fromVersion} to ${toVersion}`,
    breakingChanges: changes.breaking,
    newFeatures: changes.features,
    deprecated: changes.deprecated,
    codeExamples: await generateMigrationExamples(changes),
  };
  
  await writeMarkdownFile(`migration-${fromVersion}-to-${toVersion}.md`, guide);
}
```

## Version Selector Component

```jsx
// src/components/VersionSelector/index.js
import React from 'react';
import { useVersions, useActiveVersion } from '@docusaurus/plugin-content-docs/client';

export default function VersionSelector() {
  const versions = useVersions();
  const activeVersion = useActiveVersion();
  
  return (
    <div className="version-selector">
      <label htmlFor="version-select">Version:</label>
      <select 
        id="version-select"
        value={activeVersion.name}
        onChange={(e) => {
          const selected = versions.find(v => v.name === e.target.value);
          window.location.href = selected.path;
        }}
      >
        {versions.map(version => (
          <option key={version.name} value={version.name}>
            {version.label}
            {version.isLast && ' (Latest)'}
          </option>
        ))}
      </select>
      
      {!activeVersion.isLast && (
        <div className="version-warning">
          <strong>Note:</strong> You're viewing documentation for an older version.
          <a href={versions.find(v => v.isLast)?.path}>
            View latest version
          </a>
        </div>
      )}
    </div>
  );
}
```

## Content Synchronization

### Shared Content Strategy

Some content should be synchronized across versions:

```
shared/
├── getting-started/      # Core concepts remain similar
├── concepts/            # Architecture fundamentals
└── troubleshooting/     # Common issues

version-specific/
├── api-reference/       # Version-specific APIs
├── breaking-changes/    # Version-specific changes
└── examples/           # Version-specific code samples
```

### Content Sync Pipeline

```javascript
// scripts/sync-shared-content.js
const sharedContent = [
  'getting-started/overview.md',
  'concepts/architecture.md',
  'troubleshooting/common-issues.md'
];

async function syncSharedContent() {
  for (const file of sharedContent) {
    const masterContent = await fs.readFile(`docs/${file}`, 'utf8');
    
    // Sync to all active versions
    for (const version of activeVersions) {
      const versionFile = `versioned_docs/version-${version}/${file}`;
      await fs.writeFile(versionFile, masterContent);
    }
  }
}
```

## Search Versioning

### Version-Aware Search

```javascript
// Algolia search configuration
searchParameters: {
  facetFilters: [
    `version:${currentVersion}`,
    // Always include current version in results
    'version:current'
  ]
}

// Search UI with version filter
<SearchBox
  searchParameters={{
    facetFilters: [`version:${selectedVersion}`]
  }}
  transformItems={(items) => {
    return items.map(item => ({
      ...item,
      versionBadge: item.version !== 'current' ? item.version : null
    }));
  }}
/>
```

### Cross-Version Search Results

```jsx
// Show results from multiple versions with clear indicators
function SearchResult({ hit }) {
  return (
    <div className="search-result">
      <h3>
        <a href={hit.url}>{hit.title}</a>
        {hit.version !== 'current' && (
          <span className={`version-badge version-${hit.version}`}>
            v{hit.version}
          </span>
        )}
      </h3>
      <p>{hit.content}</p>
      {hit.version !== currentVersion && (
        <div className="version-note">
          This content is for version {hit.version}.
          <a href={`/current${hit.path}`}>View current version</a>
        </div>
      )}
    </div>
  );
}
```

## Analytics and Metrics

### Version Usage Tracking

```javascript
// Track which versions users are viewing
function trackVersionUsage() {
  gtag('event', 'version_viewed', {
    version: currentVersion,
    page_path: window.location.pathname,
    is_latest: currentVersion === latestVersion
  });
}

// Monthly analysis:
// - Version adoption rates
// - Legacy version usage
// - Migration patterns
// - Support burden by version
```

## Deployment Strategy

### Version-Specific Builds

```yaml
# .github/workflows/deploy-docs.yml
name: Deploy Documentation

on:
  push:
    branches: [main, 'v*']
    tags: ['v*']

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # Fetch all history for versioning
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build all versions
        run: |
          npm run build:version:current
          npm run build:version:5.0
          npm run build:version:4.0
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
```

### Incremental Builds

```javascript
// Only rebuild changed versions
async function incrementalBuild() {
  const changedFiles = await getChangedFiles();
  const affectedVersions = getAffectedVersions(changedFiles);
  
  for (const version of affectedVersions) {
    console.log(`Rebuilding version ${version}`);
    await buildVersion(version);
  }
  
  // Update sitemap and search index
  await updateSitemap();
  await updateSearchIndex(affectedVersions);
}
```

## Maintenance Automation

### Automated Version Cleanup

```javascript
// scripts/cleanup-old-versions.js
async function cleanupOldVersions() {
  const cutoffDate = new Date();
  cutoffDate.setFullYear(cutoffDate.getFullYear() - 2);
  
  const oldVersions = versions.filter(v => 
    new Date(v.releaseDate) < cutoffDate
  );
  
  for (const version of oldVersions) {
    // Archive to separate storage
    await archiveVersion(version);
    
    // Remove from active site
    await removeVersionFromSite(version);
    
    // Update search index
    await removeVersionFromSearch(version);
  }
}
```

### Version Health Monitoring

```javascript
// Monitor version-specific metrics
const versionMetrics = {
  buildTime: {},
  errorRate: {},
  userTraffic: {},
  searchQueries: {}
};

// Alert on version-specific issues
function monitorVersionHealth() {
  for (const version of activeVersions) {
    if (versionMetrics.errorRate[version] > threshold) {
      alertVersionIssue(version, 'High error rate');
    }
    
    if (versionMetrics.buildTime[version] > timeThreshold) {
      alertVersionIssue(version, 'Slow build time');
    }
  }
}
```

## Best Practices

### 1. Content Strategy
- Maintain migration guides for at least 2 major versions
- Keep breaking changes documentation for legacy versions
- Archive but don't delete old versions immediately

### 2. User Experience
- Clear version indicators throughout the site
- Easy version switching in navigation
- Version-specific search results
- Migration assistance tools

### 3. Maintenance
- Automate version creation and cleanup
- Monitor version usage to inform support decisions
- Regular audits of cross-version content sync
- Performance monitoring per version

### 4. SEO Considerations
- Canonical URLs pointing to latest version
- Noindex old versions to avoid duplicate content
- Version-specific sitemaps
- Proper redirect handling for moved content