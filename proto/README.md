# Protobuf Code Generation

This document explains how to generate Go code from `.proto` files in this project.

## Prerequisites

1. **Install Protocol Buffers compiler (protoc)**
   ```bash
   # macOS
   brew install protoc
   ```

2. **Install ggogo protobuf plugins**
   ```bash
    # 安装 gogo 插件 v1.3.2
    go install github.com/gogo/protobuf/protoc-gen-gogo@v1.3.2
    
    # 检查 protobuf 依赖
    go list -m all | grep protobuf
    github.com/gogo/protobuf v1.3.2

   ```

3. **Important: Use regen-network protobuf version**
   
   This project **MUST** use `github.com/regen-network/protobuf v1.3.3-alpha.regen.1` for both:
   - Code generation (protoc plugins)
   - Runtime (go.mod dependency)
   
   **DO NOT** use the standard `github.com/gogo/protobuf` as it will cause version compatibility issues.

3. **copy iavl and gogoproto to proto and modify the go_package**
   
    ```proto
    syntax = "proto3";
    package iavl;

    option go_package = "github.com/cosmos/iavl;iavl";

    message KVPair {
        bool delete = 1;
        bytes key = 2;
        bytes value = 3;
    }

    message ChangeSet {
        repeated KVPair pairs = 1;
    }

    ```
 


## Project Structure

```
proto/
├── gogoproto/
│   └── gogo.proto          # Gogoproto extensions
├── iavl/
│   └── changeset.proto     # IAVL changeset definitions
├── memiavl/
│   ├── changelog.proto    # Changelog definitions
│   └── commit_info.proto  # Commit info definitions
└── README.md              # This file
```

## Generating Go Code


###  Generate Individual Files

```bash
# Generate changelog.pb.go
protoc --gogo_out=. --gogo_opt=paths=source_relative  --proto_path=.  memiavl/changelog.proto

# Generate commit_info.pb.go  
protoc --gogo_out=. --gogo_opt=paths=source_relative  --proto_path=.  memiavl/commit_info.proto

```

### Genereate dependent cosmos/ival using same compiler

```bash
# Generate changelog.pb.go
git clone https://github.com/cosmos/iavl.git
git checkout v1.2.6 
cd iavl/proto
protoc --gogo_out=. --gogo_opt=paths=source_relative  --proto_path=.  changeset.proto

```

## 