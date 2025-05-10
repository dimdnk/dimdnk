# 📚 Git Rules

## 1. Branching Rules

- Configure `dev` branch as main by default for personal projects
- Avoid direct push to main branches such as `main` or `dev`.
- Create a new branch for each task or feature.

### Branch Naming Structure:

- New Feature: `feature/feature-name`
- Bug Fix: `bugfix/issue-description`
- Hotfix: `hotfix/critical-issue`
- Test: `test/test-description`

#### Examples:

- `feature/user-authentication`
- `bugfix/fix-login-issue`

### Adding Task Number (Optional):
Add the Jira task number at the beginning of the branch name:

#### Examples:

- `feature/123-user-authentication`
- `bugfix/456-login-error`

---

## 2. Commit Message Rules

- The commit message should be short, concise, and written in the present tense.
- Each commit should only cover a single logical change (Atomic Commit).
- Using the Conventional Commits structure is recommended.

### Suggested Commit Message Structure:
```
<type>(scope): short message
```

#### Examples:

- `feat(auth): add OTP login via SMS`
- `fix(cart): correct total price calculation`
- `refactor(db): optimize user query performance`
- `docs(api): update response schema docs`

### Suggested Change Types:

- `feat`: Adding a new feature
- `fix`: Bug fix
- `refactor`: Improving the code structure without changing functionality
- `docs`: Documentation changes
- `style`: Styling changes (no new logic)
- `test`: Adding or modifying tests

---

## 3. Merge Request Rules

- After completing each task, open a Merge Request for the relevant branch onto `dev`.
- Conflict resolution before submitting the Merge Request is the responsibility of the developer.
- Code review is performed by the team's Tech Lead / Repo Owner.
- The Team Lead or Tech Lead is responsible for adding technical comments, issues, or suggestions inside GitHub, making them trackable and transparent.
