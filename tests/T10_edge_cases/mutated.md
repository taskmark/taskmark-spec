# Edge Cases

## Whitespace Handling

- [ ]    Extra spaces in title
- [ ] Tab in title
- [ ] Normal task

## Empty and Minimal

- [ ]
- [ ] A
- [ ] 1

## Malformed (should be preserved as non-tasks)

- [] Missing space after bracket
- [y] Invalid state character
- [  ] Double space in checkbox
This is not a task
  - [ ] Orphan subtask (no parent task)

## Duplicate Metadata

- [ ] Task +Project1 +Project2 due:2024-03-10 due:2024-03-15
- [ ] Task @alice @bob @alice #tag #tag

## Deeply Nested

- [ ] Level 1
  - [ ] Level 2
    - [x] Level 3 done:2024-03-10
      - [ ] Level 4
        - [ ] Level 5

## Long Content

- [.] This is a very long task title that goes on and on and on and contains lots of words to test how the parser handles extremely long lines that might wrap in editors or cause buffer issues in naive implementations started:2024-03-10

## Mixed Indentation

- [ ] Parent with spaces
 	- [ ] Child with tab
  - [ ] Child with 2 spaces
    - [ ] Child with 4 spaces

## Ambiguous Target (Error Case)

- [ ] Duplicate title task
- [ ] Duplicate title task
