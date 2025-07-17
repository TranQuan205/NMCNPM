/**
     * Chức năng thêm nhiệm vụ mới
     *
     * @param title Tiêu đề nhiệm vụ.
     * @param description Mô tả nhiệm vụ.
     * @param dueDateStr Ngày đến hạn (định dạng YYYY-MM-DD).
     * @param priorityLevel Mức độ ưu tiên ("Thấp", "Trung bình", "Cao").
     * @param isRecurring Boolean có phải là nhiệm vụ lặp lại không.
     * @return JSONObject của nhiệm vụ đã thêm, hoặc null nếu có lỗi.
     */
    public JSONObject addNewTaskWithViolations(String title, String description,
                                                String dueDateStr, String priorityLevel,
                                                boolean isRecurring) {

        if (!isValidTitle(title)) {
            System.out.println("Lỗi: Tiêu đề không được để trống.");
            return null;
        }

        if (dueDateStr == null || dueDateStr.trim().isEmpty()) {
            System.out.println("Lỗi: Ngày đến hạn không được để trống.");
            return null;
        }
        LocalDate dueDate = parseDueDate(dueDateStr);
            if (dueDate == null) {
            System.out.println("Lỗi: Ngày đến hạn không hợp lệ.");
            return null;
            }

        if (!isValidPriority(priorityLevel)) {
            System.out.println("Lỗi: Mức độ ưu tiên không hợp lệ.");
            return null;
        }


        // Tải dữ liệu
        JSONArray tasks = loadTasksFromDb();

        // Kiểm tra trùng lặp
        if (isDuplicateTask(tasks, title, dueDate.format(DATE_FORMATTER))) {
            System.out.println("Lỗi: Nhiệm vụ đã tồn tại...");
            return null;
        }


        String taskId = String.valueOf(tasks.size() + 1);

        JSONObject newTask = new JSONObject();
        newTask.put("id", taskId);
        newTask.put("title", title);
        newTask.put("description", description);
        newTask.put("due_date", dueDate.format(DATE_FORMATTER));
        newTask.put("priority", priorityLevel);
        newTask.put("status", "Chưa hoàn thành");
        newTask.put("created_at", getCurrentTimestamp());
        newTask.put("last_updated_at", getCurrentTimestamp());

        tasks.add(newTask);