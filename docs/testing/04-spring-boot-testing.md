---
title: Spring Boot Testing
parent: Testing
nav_order: 4
---

# Spring Boot Testing

---

# 1. Why Spring Boot Testing is Different

In pure unit testing:
- We test plain Java classes.
- No Spring context is loaded.

In Spring Boot applications:
- Beans are managed by Spring.
- Dependencies are injected automatically.
- Web layer, security, JPA, etc. are auto-configured.

So we need different testing strategies depending on what layer we test.

---

# 2️.  Spring Boot Testing Strategy (Layer-Based)

| Layer        | Testing Approach |
|-------------|------------------|
| Controller  | @WebMvcTest |
| Service     | Unit Test + Mockito |
| Repository  | @DataJpaTest |
| Full App    | @SpringBootTest |

This avoids loading unnecessary parts of the application.

---

# 3️. @SpringBootTest

Loads the full Spring application context.

```java
@SpringBootTest
class ApplicationTest {
}
```

**What It Does:**           

- Starts entire Spring container
- Loads all beans
- Applies configuration
- Can start embedded server

Web Environment Options

> @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)

Available modes:

- MOCK → Mock servlet environment (no real server)
- RANDOM_PORT → Starts server on random port
- DEFINED_PORT → Uses configured port
- NONE → No web environment

When To Use

- Full integration tests
- End-to-end backend testing
- Testing configuration behavior
- ⚠ Slowest type of Spring test.

---

# 4️. @WebMvcTest (Controller Testing)

Used to test only the web layer.

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
}
```

**What It Loads:**           

- Controller
- MockMvc
- Jackson
- MVC infrastructure

**What It Does NOT Load**        

- Service layer
- Repository layer
- Full application context

You must mock dependencies.

**Example**     

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void getUser_shouldReturnUser() throws Exception {

        when(userService.getUser(1L))
                .thenReturn(new User(1L, "John"));

        mockMvc.perform(get("/users/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("John"));
    }
}
```

# 5️. MockMvc

MockMvc simulates HTTP requests without starting a real server.

Example:

```
mockMvc.perform(get("/users/1")).andExpect(status().isOk());
```

Common methods:

- get()
- post()
- put()
- delete()

Common assertions:

- status()
- jsonPath()
- content()
- header()

---

# 6️. @DataJpaTest (Repository Testing)

Used to test JPA layer only.

```java
@DataJpaTest
class UserRepositoryTest {
}
```

**What It Loads**       

- JPA components
- EntityManager
- Repositories
- In-memory database (H2 by default)


## Example

```java
@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void saveUser_shouldPersist() {

        User user = new User(null, "John");
        userRepository.save(user);

        Optional<User> result = userRepository.findById(user.getId());

        assertTrue(result.isPresent());
    }
}
```

---

# 7️. @MockBean vs @Mock

| Annotation | Used In                       |
| ---------- | ----------------------------- |
| @Mock      | Pure unit test (Mockito only) |
| @MockBean  | Spring Boot test              |

`@MockBean` replaces a bean inside Spring context.

**Example:**

```
@MockBean
private UserService userService;
```

Used inside:

- @WebMvcTest
- @SpringBootTest

---

# 8️. Testing with Security

If Spring Security is enabled, endpoints require authentication.

For controller tests:

> @AutoConfigureMockMvc(addFilters = false)

This disables security filters.

Or use:

> @WithMockUser

Example:

```java
@Test
@WithMockUser(roles = "ADMIN")
void securedEndpoint_shouldAllowAccess() {
}
```

---

# 9️. Full Integration Example

```java

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void fullFlowTest() {

        ResponseEntity<User> response =
                restTemplate.getForEntity("/users/1", User.class);

        assertEquals(HttpStatus.OK, response.getStatusCode());
    }
}
```

This starts real server on random port.

---

# 10. Best Practices

- Avoid @SpringBootTest unless needed.
- Mock only external dependencies.
- Keep controller tests focused on request/response.
- Keep service tests pure unit tests.
- Use TestContainers for realistic DB tests.
- Avoid hitting real external APIs.


# 11. Professional Microservice Testing Flow

- Controller → @WebMvcTest
- Service → Unit Test (Mockito)
- Repository → @DataJpaTest
- Full API Flow → @SpringBootTest
- External DB → TestContainers
- External APIs → MockServer / WireMock

---

```java

package com.branch.controller;

import com.branch.entity.Branch;
import com.branch.entity.BranchDTO;
import com.branch.service.AsyncUseCase;
import com.branch.service.BranchService;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.bean.override.mockito.MockitoBean;
import org.springframework.test.web.servlet.MockMvc;

import java.util.List;

import static org.hamcrest.Matchers.is;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.put;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(BranchController.class)
class BranchController_WebMvcTest {

    @Autowired
    private MockMvc mockMvc;

    @MockitoBean
    private BranchService branchService;

    @MockitoBean
    private AsyncUseCase asyncUseCase;

    @Autowired
    private ObjectMapper objectMapper;

    // ===============================
    // CREATE BRANCH
    // ===============================

    @Test
    @DisplayName("POST /api/branches - should create branch")
    void createBranch_shouldReturnCreated() throws Exception {

        BranchDTO dto = new BranchDTO();
        dto.setBranchCode("BR001");
        dto.setName("Main Branch");

        Branch saved = new Branch();
        saved.setId(1L);
        saved.setBranchCode("BR001");
        saved.setName("Main Branch");

        when(branchService.createBranch(any(BranchDTO.class)))
                .thenReturn(saved);

        mockMvc.perform(post("/api/branches")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(dto)))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.id").value(1L))
                .andExpect(jsonPath("$.branchCode").value("BR001"))
                .andExpect(jsonPath("$.name").value("Main Branch"));

        verify(branchService).createBranch(any(BranchDTO.class));
    }

    // ===============================
    // GET BY BRANCH CODE
    // ===============================

    @Test
    void getBranchByBranchCode_shouldReturnBranch() throws Exception {

        Branch branch = new Branch();
        branch.setId(1L);
        branch.setBranchCode("BR001");
        branch.setName("Main Branch");

        when(branchService.getBranchByBranchCode("BR001"))
                .thenReturn(branch);

        mockMvc.perform(get("/api/branches/BR001"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.branchCode").value("BR001"))
                .andExpect(jsonPath("$.name").value("Main Branch"));

        verify(branchService).getBranchByBranchCode("BR001");
    }

    // ===============================
    // GET ALL
    // ===============================

    @Test
    void getAllBranches_shouldReturnList() throws Exception {

        Branch branch1 = new Branch(1L,"BR001", "BR001", "Main");
        Branch branch2 = new Branch(2L, "BR002","BR002", "City");

        when(branchService.getAllBranches())
                .thenReturn(List.of(branch1, branch2));

        mockMvc.perform(get("/api/branches"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.size()", is(2)))
                .andExpect(jsonPath("$[0].branchCode").value("BR001"))
                .andExpect(jsonPath("$[1].branchCode").value("BR002"));

        verify(branchService).getAllBranches();
    }

    // ===============================
    // UPDATE
    // ===============================

    @Test
    void updateBranch_shouldReturnUpdated() throws Exception {

        BranchDTO dto = new BranchDTO();
        dto.setBranchCode("BR001");
        dto.setName("Updated Branch");

        Branch updated = new Branch(1L, "Updated Branch", "BR001","CTR");

        when(branchService.updateBranch(eq(1L), any(BranchDTO.class)))
                .thenReturn(updated);

        mockMvc.perform(put("/api/branches/1")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(dto)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("Updated Branch"));

        verify(branchService).updateBranch(eq(1L), any(BranchDTO.class));
    }

    // ===============================
    // DELETE
    // ===============================

    @Test
    void deleteBranch_shouldReturnNoContent() throws Exception {

        mockMvc.perform(delete("/api/branches/1"))
                .andExpect(status().isNoContent());

        verify(branchService).deleteBranch(1L);
    }

    // ===============================
    // EXISTS
    // ===============================

    @Test
    void isBranchExists_shouldReturnTrue() throws Exception {

        when(branchService.isBranchExists("BR001"))
                .thenReturn(true);

        mockMvc.perform(get("/api/branches/exists/BR001"))
                .andExpect(status().isOk())
                .andExpect(content().string("true"));

        verify(branchService).isBranchExists("BR001");
    }

    // ===============================
    // ASYNC ENDPOINT
    // ===============================

    @Test
    void asyncEndpoint_shouldReturnOk() throws Exception {

        mockMvc.perform(get("/api/branches/async"))
                .andExpect(status().isOk())
                .andExpect(content().string("OK"));

        verify(asyncUseCase).sendEmail(anyString());
    }

    // ===============================
    // VALIDATION FAILURE
    // ===============================

    @Test
    void createBranch_invalidInput_shouldReturnBadRequest() throws Exception {

        BranchDTO dto = new BranchDTO(); // empty → should fail validation

        mockMvc.perform(post("/api/branches")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(dto)))
                .andExpect(status().isBadRequest());
    }

}
```



