## Shiro 用户权限管理学习心得 ##

**Shiro** 是一个简单易用的java安全框架，其富含执行权限授权、认证，加密和session管理。用户角色权限管理几乎在所有的项目后台中都会用到，
我们通常说的用户角色权限管理主要涉及到5个用户表：用户表、角色表、权限表、用户角色表、角色权限表；

对这5个表做下剖析：

- 用户表：包含用户信息，用户唯一标识；
- 角色表：包含角色信息，角色唯一标识；
- 权限表：包含权限路径，权限唯一标识；
- 用户角色表：用户<->角色，多对多关系的表示；
- 角色权限表：角色<->权限，多对多关系的表示；


### 下文是一个简单shiro框架的java实现例子 ###

**使用框架**:spring+shiro+mybatis

**包结构**：

	----com.lee.shiro.controller
	----com.lee.shiro.entity
	----com.lee.shiro.service
	----com.lee.shiro.service.impl
	----com.lee.shiro.security

说明：controller前端控制器，entity实体类包，service业务接口包，service.impl业务接口实现包，security安全机制包。



#### entity包各部源码示例 ####

	/**
	 * 
	 */
	package com.shiro.demo.entity;
	
	/**
	 * Description 
	 * @author lijiaxi 
	 * @date 2017-11-13
	 */
	public class User {
		private Long id;
		private String username;
		private String password;
		private String salt;
	
		private Boolean locked = Boolean.FALSE;
	
		public User() {
		}
	
		public User(String username, String password) {
			this.username = username;
			this.password = password;
		}
	
		public Long getId() {
			return id;
		}
	
		public void setId(Long id) {
			this.id = id;
		}
	
		public String getUsername() {
			return username;
		}
	
		public void setUsername(String username) {
			this.username = username;
		}
	
		public String getPassword() {
			return password;
		}
	
		public void setPassword(String password) {
			this.password = password;
		}
	
		public String getSalt() {
			return salt;
		}
	
		public void setSalt(String salt) {
			this.salt = salt;
		}
	
		public String getCredentialsSalt() {
			return username + salt;
		}
	
		public Boolean getLocked() {
			return locked;
		}
	
		public void setLocked(Boolean locked) {
			this.locked = locked;
		}
	
		@Override
		public boolean equals(Object o) {
			if (this == o) return true;
			if (o == null || getClass() != o.getClass()) return false;
	
			User user = (User) o;
	
			if (id != null ? !id.equals(user.id) : user.id != null) return false;
	
			return true;
		}
	
		@Override
		public int hashCode() {
			return id != null ? id.hashCode() : 0;
		}
	
		@Override
		public String toString() {
			return "User{" +
					"id=" + id +
					", username='" + username + '\'' +
					", password='" + password + '\'' +
					", salt='" + salt + '\'' +
					", locked=" + locked +
					'}';
		}
	
	}


	/**
	 * 
	 */
	package com.shiro.demo.entity;
	
	/**
	 * Description 
	 * @author lijiaxi 
	 * @date 2017-11-10
	 */
	public class Role {
	 private Long id;
	    private String role; //角色标识 程序中判断使用,如"admin"
	    private String description; //角色描述,UI界面显示使用
	    private Boolean available = Boolean.FALSE; //是否可用,如果不可用将不会添加给用户

	    public Role() {
	    }

	    public Role(String role, String description, Boolean available) {
	        this.role = role;
	        this.description = description;
	        this.available = available;
	    }

	    public Long getId() {
	        return id;
	    }

	    public void setId(Long id) {
	        this.id = id;
	    }

	    public String getRole() {
	        return role;
	    }

	    public void setRole(String role) {
	        this.role = role;
	    }

	    public String getDescription() {
	        return description;
	    }

	    public void setDescription(String description) {
	        this.description = description;
	    }

	    public Boolean getAvailable() {
	        return available;
	    }

	    public void setAvailable(Boolean available) {
	        this.available = available;
	    }

	    @Override
	    public boolean equals(Object o) {
	        if (this == o) return true;
	        if (o == null || getClass() != o.getClass()) return false;

	        Role role = (Role) o;

	        if (id != null ? !id.equals(role.id) : role.id != null) return false;

	        return true;
	    }

	    @Override
	    public int hashCode() {
	        return id != null ? id.hashCode() : 0;
	    }

	    @Override
	    public String toString() {
	        return "Role{" +
	                "id=" + id +
	                ", role='" + role + '\'' +
	                ", description='" + description + '\'' +
	                ", available=" + available +
	                '}';
	    }
	}


	
	/**
	 * 
	 */
	package com.shiro.demo.entity;
	
	/**
	 * Description 
	 * @author lijiaxi 
	 * @date 2017-11-13
	 */
	public class Permission {
		 private Long id;
		    private String permission; //权限标识 程序中判断使用,如"user:create"
		    private String description; //权限描述,UI界面显示使用
		    private Boolean available = Boolean.FALSE; //是否可用,如果不可用将不会添加给用户
	
		    public Permission() {
		    }
	
		    public Permission(String permission, String description, Boolean available) {
		        this.permission = permission;
		        this.description = description;
		        this.available = available;
		    }
	
		    public Long getId() {
		        return id;
		    }
	
		    public void setId(Long id) {
		        this.id = id;
		    }
	
		    public String getPermission() {
		        return permission;
		    }
	
		    public void setPermission(String permission) {
		        this.permission = permission;
		    }
	
		    public String getDescription() {
		        return description;
		    }
	
		    public void setDescription(String description) {
		        this.description = description;
		    }
	
		    public Boolean getAvailable() {
		        return available;
		    }
	
		    public void setAvailable(Boolean available) {
		        this.available = available;
		    }
	
		    @Override
		    public boolean equals(Object o) {
		        if (this == o) return true;
		        if (o == null || getClass() != o.getClass()) return false;
	
		        Permission role = (Permission) o;
	
		        if (id != null ? !id.equals(role.id) : role.id != null) return false;
	
		        return true;
		    }
	
		    @Override
		    public int hashCode() {
		        return id != null ? id.hashCode() : 0;
		    }
	
		    @Override
		    public String toString() {
		        return "Role{" +
		                "id=" + id +
		                ", permission='" + permission + '\'' +
		                ", description='" + description + '\'' +
		                ", available=" + available +
		                '}';
		    }
	}

	
	/**
	 * 
	 */
	package com.shiro.demo.entity;
	
	/**
	 * Description 
	 * @author lijiaxi 
	 * @date 2017-11-13
	 */
	public class UserRole {
	
	    private Long userId;
	    private Long roleId;
	
	    public Long getUserId() {
	        return userId;
	    }
	
	    public void setUserId(Long userId) {
	        this.userId = userId;
	    }
	
	    public Long getRoleId() {
	        return roleId;
	    }
	
	    public void setRoleId(Long roleId) {
	        this.roleId = roleId;
	    }
	
	    @Override
	    public boolean equals(Object o) {
	        if (this == o) return true;
	        if (o == null || getClass() != o.getClass()) return false;
	
	        UserRole userRole = (UserRole) o;
	
	        if (roleId != null ? !roleId.equals(userRole.roleId) : userRole.roleId != null) return false;
	        if (userId != null ? !userId.equals(userRole.userId) : userRole.userId != null) return false;
	
	        return true;
	    }
	
	    @Override
	    public int hashCode() {
	        int result = userId != null ? userId.hashCode() : 0;
	        result = 31 * result + (roleId != null ? roleId.hashCode() : 0);
	        return result;
	    }
	
	    @Override
	    public String toString() {
	        return "UserRole{" +
	                "userId=" + userId +
	                ", roleId=" + roleId +
	                '}';
	    }
	}

	
	/**
	 * 
	 */
	package com.shiro.demo.entity;
	
	/**
	 * Description 
	 * @author lijiaxi 
	 * @date 2017-11-13
	 */
	public class RolePermssion {
		private Long roleId;
	    private Long permissionId;
	
	    public Long getRoleId() {
	        return roleId;
	    }
	
	    public void setRoleId(Long roleId) {
	        this.roleId = roleId;
	    }
	
	    public Long getPermissionId() {
	        return permissionId;
	    }
	
	    public void setPermissionId(Long permissionId) {
	        this.permissionId = permissionId;
	    }
	
	    @Override
	    public boolean equals(Object o) {
	        if (this == o) return true;
	        if (o == null || getClass() != o.getClass()) return false;
	
	        RolePermssion that = (RolePermssion) o;
	
	        if (permissionId != null ? !permissionId.equals(that.permissionId) : that.permissionId != null) return false;
	        if (roleId != null ? !roleId.equals(that.roleId) : that.roleId != null) return false;
	
	        return true;
	    }
	
	    @Override
	    public int hashCode() {
	        int result = roleId != null ? roleId.hashCode() : 0;
	        result = 31 * result + (permissionId != null ? permissionId.hashCode() : 0);
	        return result;
	    }
	
	    @Override
	    public String toString() {
	        return "RolePermssion{" +
	                "roleId=" + roleId +
	                ", permissionId=" + permissionId +
	                '}';
	    }
		
	}

对各个实体类的属性值进行简要说明：








Don't guess if your [hyperlink syntax](http://markdownpad.com) is correct; LivePreview will show you exactly what your document looks like every time you press a key.

### Make it your own ###

Fonts, color schemes, layouts and stylesheets are all 100% customizable so you can turn MarkdownPad into your perfect editor.

### A robust editor for advanced Markdown users ###

MarkdownPad supports multiple Markdown processing engines, including standard Markdown, Markdown Extra (with Table support) and GitHub Flavored Markdown.

With a tabbed document interface, PDF export, a built-in image uploader, session management, spell check, auto-save, syntax highlighting and a built-in CSS management interface, there's no limit to what you can do with MarkdownPad.
