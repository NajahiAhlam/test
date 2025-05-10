    .assigne(userRepository.findByEmail(c.getAssigne())
             .orElseThrow(() -> new RuntimeException("User not found: " + c.getAssigne())))
