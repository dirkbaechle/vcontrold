# SPDX-FileCopyrightText: 2017-2021 Tobias Leupold <tl@l3u.de>
#
# SPDX-License-Identifier: BSD-3-Clause

# Generate classic *roff manual pages from ReStructuredText.
# This requires Python's Docutils (http://docutils.sourceforge.net/)

cmake_minimum_required(VERSION 3.2.0)

set(RST2MAN_OPTS)

set(MANUALS vcontrold)
if(VCLIENT)
    list(APPEND MANUALS vclient)
endif(VCLIENT)
if(VSIM)
    list(APPEND MANUALS vsim)
endif(VSIM)

foreach(MANUAL IN LISTS MANUALS)
    set(MANPAGE_OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${MANUAL}.1)
    set(MANPAGE_SOURCE ${CMAKE_CURRENT_SOURCE_DIR}/${MANUAL}.rst)
    add_custom_command(OUTPUT ${MANPAGE_OUTPUT}
        COMMAND ${RST2MAN} ${RST2MAN_OPTS} ${MANPAGE_SOURCE} ${MANPAGE_OUTPUT}
        DEPENDS ${MANPAGE_SOURCE}
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        COMMENT "Converting ${MANUAL}.rst from ReStructuredText into manpage ${MANUAL}.1"
        VERBATIM
    )
    list(APPEND MANPAGES_LIST ${MANPAGE_OUTPUT})
endforeach()

add_custom_target(man ALL DEPENDS ${MANPAGES_LIST})
install(FILES ${MANPAGES_LIST} DESTINATION ${CMAKE_INSTALL_PREFIX}/share/man/man1)
