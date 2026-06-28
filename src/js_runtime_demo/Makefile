CXX := g++
TARGET := demo
SRC := main.cc

CXXFLAGS := -g -std=c++17
LDFLAGS := -ldl -pthread

V8_DIR ?= v8
V8_INCLUDE := $(V8_DIR)/include
V8_LIB_DIR := $(V8_DIR)/out.gn/x64.release.sample/obj
LOCAL_V8_LIB := $(firstword $(wildcard $(V8_LIB_DIR)/libv8_monolith.a $(V8_LIB_DIR)/libv8_monolith.so))

LIBNODE_PC := $(shell command -v pkg-config >/dev/null 2>&1 && pkg-config --exists libnode && echo yes)
HAS_SYSTEM_NODE_HEADERS := $(wildcard /usr/include/node/v8.h)

ifeq ($(LOCAL_V8_LIB),)
	ifeq ($(LIBNODE_PC),yes)
		CXXFLAGS += $(shell pkg-config --cflags libnode)
		LDFLAGS += $(shell pkg-config --libs libnode)
		BUILD_ENV := system libnode (pkg-config)
	else ifneq ($(HAS_SYSTEM_NODE_HEADERS),)
		CXXFLAGS += -I/usr/include/node
		LDFLAGS += -lnode
		BUILD_ENV := system libnode (/usr/include/node)
	else
		BUILD_ENV := missing
	endif
else
	CXXFLAGS += -I$(V8_INCLUDE)
	CXXFLAGS += -DV8_COMPRESS_POINTERS -DV8_ENABLE_SANDBOX
	LDFLAGS += -L$(V8_LIB_DIR) -lv8_monolith
	BUILD_ENV := local V8 ($(V8_DIR))
endif

.PHONY: build clean

build:
	@if [ "$(BUILD_ENV)" = "missing" ]; then \
		echo "No usable V8 environment found."; \
		echo "Option 1 (local V8): place V8 under ./v8 with include/ and out.gn/x64.release.sample/obj/libv8_monolith.*"; \
		echo "Option 2 (system libnode): install libnode-dev so /usr/include/node and -lnode are available"; \
		exit 1; \
	fi
	@echo "Building with $(BUILD_ENV)"
	$(CXX) $(CXXFLAGS) $(SRC) -o $(TARGET) $(LDFLAGS)

clean:
	rm -f $(TARGET)
