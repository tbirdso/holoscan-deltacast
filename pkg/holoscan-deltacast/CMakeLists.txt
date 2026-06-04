# SPDX-FileCopyrightText: Copyright (c) 2026 Deltacast / NVIDIA. All rights reserved.
# SPDX-License-Identifier: Apache-2.0

# This file is entered via add_holohub_package(holoscan-deltacast ...) in
# pkg/CMakeLists.txt, which only fires when PKG_holoscan_deltacast is ON.
include(holohub_configure_deb)

holohub_configure_deb(
    NAME        "holoscan-deltacast"
    DESCRIPTION "${PROJECT_DESCRIPTION}"
    VERSION     "${PROJECT_VERSION}"
    VENDOR      "Deltacast"
    CONTACT     "support@deltacast.tv"
    DEPENDS     "holoscan (>= 4.2.0)"
)
