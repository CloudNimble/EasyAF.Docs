# Static Site Generators Research

This section contains comprehensive research on various static site generator options for the SimpleMessageBus documentation site.

## Evaluated Options

1. **[Docusaurus](docusaurus-analysis.md)** - Meta's React-based documentation framework
2. **[VitePress](vitepress-analysis.md)** - Vue-powered static site generator
3. **[DocFX](docfx-analysis.md)** - Microsoft's .NET-specific documentation tool
4. **[Alternatives](alternatives-comparison.md)** - Other options considered

## Key Evaluation Criteria

- **C# XML Documentation Support** - Ability to integrate with .NET XML comments
- **Learn.microsoft.com Aesthetics** - Visual similarity to Microsoft's documentation
- **Developer Experience** - Ease of use and maintenance
- **Search Capabilities** - Built-in or easily integrated search
- **Performance** - Build speed and runtime performance
- **Community & Support** - Active development and community
- **Extensibility** - Plugin system and customization options

## Summary of Findings

| Feature | Docusaurus | VitePress | DocFX |
|---------|------------|-----------|--------|
| C# XML Support | Via plugins | Limited | Native |
| Modern UI | Excellent | Excellent | Limited |
| Search | Algolia integration | Local search | Basic |
| Performance | Very Good | Excellent | Good |
| Community | Large | Growing | .NET-specific |
| Customization | Extensive | Good | Limited |
| Learning Curve | Moderate | Low | High |

## Final Decision

**[Docusaurus](final-recommendation.md)** was selected as the best option for SimpleMessageBus documentation.

### Key Reasons:
1. **Best balance** of modern UI and extensibility
2. **Strong plugin ecosystem** for C# integration
3. **Used by Microsoft** for many projects
4. **Excellent search** capabilities with Algolia
5. **Active community** and ongoing development

## Alternative Considerations

While DocFX has native C# support, its limitations in modern UI/UX and the fact that Microsoft has moved away from it for their own documentation made it less suitable for creating a learn.microsoft.com-style experience.

VitePress offers excellent performance but lacks the mature ecosystem needed for complex C# documentation integration.

## Next Steps

See the [technical architecture](../02-technical-architecture/) section for implementation details of the Docusaurus-based solution.