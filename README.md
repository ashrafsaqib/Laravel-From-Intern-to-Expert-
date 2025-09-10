# Laravel Common Mistakes & Best Practices

Welcome to our collection of common mistakes and best practices for Laravel developers. Whether you're a new developer learning the framework or an experienced developer looking for a refresher, this repository aims to provide clear, concise examples of how to write better, more maintainable Laravel code.

Each topic includes a "bad code" example, demonstrating a common mistake, and a "good code" example, showing the recommended best practice.

## Topics Covered

### Architecture
- [✓] [Fat Controllers](./controllers/fat-controllers.md)
- [✓] [Validation in Controllers](./controllers/validation-in-controllers.md)
- [✓] [Hardcoding Configuration Values](./architecture/hardcoding-values.md)
- [✓] [Not Using Dependency Injection](./architecture/dependency-injection.md)
- [✓] [Creating Unnecessary Middlewares](./middlewares/unnecessary-middlewares.md)

### Database & Eloquent
- [✓] [N+1 Query Problem](./performance/n-plus-one-query-problem.md)
- [✓] [Forgetting `select()` for Columns](./eloquent/eloquent-select.md)
- [✓] [Ignoring `chunk()` for Large Datasets](./eloquent/eloquent-chunk.md)
- [✓] [Not Using Eloquent Scopes](./eloquent/eloquent-scopes.md)
- [✓] [Not Using Collection Methods](./eloquent/eloquent-collections.md)
- [✓] [Missing Accessors & Mutators](./eloquent/eloquent-accessors-mutators.md)
- [✓] [Mass Assignment Vulnerability](./models/mass-assignment.md)
- [✓] [Incorrect Relationship Definitions](./models/incorrect-relationships.md)
- [✓] [Missing Foreign Key Constraints](./migrations/missing-foreign-key-constraints.md)

### Routes
- [✓] [Not Using Route Model Binding](./routes/route-model-binding.md)
- [✓] [Not Using Route Groups](./routes/route-groups.md)
- [✓] [Using URLs instead of Named Routes](./routes/named-routes.md)

### Blade Templates
- [✓] [Putting Logic in Views](./blade/blade-logic.md)
- [✓] [Not Using Blade Components](./blade/blade-components.md)
- [✓] [Using `@include` in a Loop vs `@each`](./blade/blade-include-vs-each.md)

### Testing
- [✓] [Not Using Model Factories](./testing/testing-factories.md)
- [✓] [Forgetting to Refresh the Database in Tests](./testing/testing-refresh-database.md)

# Roadmap
This project is a work in progress. We welcome contributions and suggestions for new topics!

## Laravel Best Practices Audit Tool VS Code plugin
Additionally, if you're looking for an efficient way to streamline the code review process, I'm also working on an Audit Tool VS Code plugin. This plugin automates the evaluation of the aforementioned checklist, swiftly pinpointing any issues or deviations from best practices within your Laravel projects. By incorporating this tool into your development workflow, you can significantly enhance the efficiency and accuracy of your code reviews, ensuring that your projects meet the highest standards of maintainability and standardization.
