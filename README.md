# Hack Day Activity

# Optimizing Grade Sync: A Smarter Way to Handle Course Deletion & Tokens between eAssessment and ELMS.

## 🎯 Objective:

Improve the integration between eAssessment and ELMS by handling errors more effectively when:

    1. A course is deleted in eAss, preventing ELMS from attempting to fetch/sync grades for non-existent courses.
    
    2. A token is regenerated, ensuring ELMS receives clear feedback when authentication fails due to invalid tokens.

> The goal is to brainstorm, prototype, and implement a solution that make the scheduled task more robust, informative, and responsive against failures.


## 📌 The problem:

 When a course is deleted in eAss or a token is regenerated, ELMS is not notified of these changes. As a result, the grade sync process in ELMS continues attempting to fetch grades for deleted courses or those with invalid tokens.

On the eAss side, there are no conditions in place to handle these scenarios gracefully. If a request is made to fetch grades for a deleted course, it triggers a database error instead of failing cleanly.

Similarly, when fetching grades for a course with an invalid token, eAss does throw an exception, but it does not provide sufficient details explaining why the token is invalid, making debugging and resolution more difficult.

## 🚀  Expectation

* The aim is to improve error handling and provide clear feedback when a grade sync is done.
* Solutions should focus on graceful failures and meaningful logs/messages on both sides.
* If time allows, explore potential improvements in the grade sync process, including optimizing database performance and addressing any inefficiencies.

## Hackday Journey
 
 1. Kikoff & Context 🚀
 2. Team Brainstorming 💡
 3. Setting up integration eAss vs ELMS locally 🛠️ 
 4. Prototyping & Implementation 🎯 (create a branch for POC)
 5. Lunch - Pizza 🍕
 6. Testing & debugging ✅- Run the schedule task with:
    
    Course deleted
    
    Invalid token
    
    Normal sync
 7. Wrap-up 😊
 


## 🛠️ Outcome:

By the end of Hack Day, we should have a more resilient gradesync, deeper knowledge of the integration with ELMS, and some great ideas for future enhancements. Stories to follow up on improvements are welcomed too. 🚀



# Apply where possible the following principles

## Single Responsibility Principle (SRP)
Each function or class should have one reason to change.

✅ Keep logic for handling deleted courses separate from handling invalid tokens.

✅ Separate concerns: API communication, error handling, and logging should not be mixed.

## Fail Fast & Graceful Degradation
Detect errors early and fail meaningfully.

✅ Validate course existence before making a request.

✅ If a token is invalid, return a clear, actionable error message instead of generic exceptions.

✅ Avoid crashing the entire scheduled task due to a single failure.


## DRY (Don’t Repeat Yourself)
Avoid duplication; reuse existing functions where possible.

✅ If error logging is needed in multiple places, create a centralized logging function.

✅ Reuse Moodle’s existing API and validation functions rather than writing new ones from scratch.


## Robust Logging & Observability
Good logs should tell a story, not just state errors.

✅ Log enough information to diagnose failures (e.g., Course ID, Request URL, Error Details).

✅ Use different log levels (INFO, WARNING, ERROR) for clarity.

✅ Make logs human-readable and actionable for debugging.

## Minimize Database Load & Optimize Queries
Be mindful of performance.

✅ Avoid unnecessary DB queries inside loops.

✅ Use batch processing when handling multiple courses.

✅ Consider caching responses for frequently requested data.

## Example

Avoid excessive queries in loops

Bad Example (Query inside a loop – inefficient)

```
foreach ($courseids as $courseid) {
    $course = $DB->get_record('course', ['id' => $courseid]);
    process_course_blah($course);
}
```

✅ Good Example (Single batch query – optimized)

```
$courseids_string = implode(',', $courseids);
$courses = $DB->get_records_sql(
    "SELECT * FROM {course} WHERE id IN ($courseids_string)");

foreach ($courses as $course) {
    process_course_blah($course);
}
```
