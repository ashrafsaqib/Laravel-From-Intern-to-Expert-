# Laravel Best Practices Checklist and Audit

Whether you're a developer, project manager, or a curious code reviewer, this comprehensive checklist can be utilized to evaluate your Laravel projects before delivery, ensuring that the codebase is maintainable, standardized, and meets the best practices of the Laravel framework.

## Routes
1. ✗ Not Using Inline Functions ✗
2. ✓ Using Namespaces ✓
3. ✓ Using Prefixes ✓
4. ✓ Using Grouping ✓
5. ✓ Using named routes and use routes names in views instead of url ✓
6. ✓ Combine / Group 'use' Controller Statements ✓
7. ✓ Use Route :: resource and avoid ✗ lengthy routes files with separate functions ✗
8. ✓ Use Route model binding ✓
9. ✗ Avoid using id in function parameters ✓ use model binding ✓

## Migrations
1. ✗ No camelCase column Names ✗ 
2. ✓ use snake_case ✓
3. ✓ Use forign keyword when tables are using foriegn keys ✓
4. ✓ Tables should be plural ✓ 
5. ✓ use onDelete and onUpdate while using forign key constraints to maintain data integrity ✓
6. ✓ check if down method is defined properly to roll back migration completely ✓ 

## Models
1. ✓ Use Singular name for model
2. ✓ Use PascalCase for File Names and Model names
3. ✓ Use fillable private property
4. 

# Roadmap
This project is not finished or released, its in progress 


## Laravel Best Practices Audit Tool VS Code plugin
Additionally, if you're looking for an efficient way to streamline the code review process, I'm also working on an Audit Tool VS Code plugin. This plugin automates the evaluation of the aforementioned checklist, swiftly pinpointing any issues or deviations from best practices within your Laravel projects. By incorporating this tool into your development workflow, you can significantly enhance the efficiency and accuracy of your code reviews, ensuring that your projects meet the highest standards of maintainability and standardization.
