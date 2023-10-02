  
    
    @GetMapping("down/{filename:.+}/{id}")
    public ResponseEntity<Resource> downloadFile(HttpServletRequest request, @PathVariable String filename, @PathVariable String id) {
        CustomLogger.log("INFO", "File requested " + filename);
        Resource resource = loader.loadFileAsResource(filename);
        CustomLogger.log("INFO", "File found " + filename);

        // Try to determine file's content type
        String contentType = null;
        try {
            contentType = request.getServletContext().getMimeType(resource.getFile().getAbsolutePath());
        } catch (IOException ex) {
            CustomLogger.log("ERROR", "Could not determine file type.");
        }

        // Fallback to the default content type if type could not be determined
        if (contentType == null) {
            contentType = "application/octet-stream";
        }

        return ResponseEntity.ok()
                .contentType(MediaType.parseMediaType(contentType))
                .header("id", id)
                .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
                .body(resource);

    }
