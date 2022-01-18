cmake_minimum_required( VERSION 3.13 )

project( scitokens_pam CXX C )

add_library( pam_scitokens SHARED 
              account_map.c 
              account_map.h
              base64.c 
              base64.h
              client.c 
              client.h 
              config.c 
              config.h 
              debug.c 
              debug.h 
              globus_auth.c 
              globus_auth.h 
              http.c 
              http.h 
              identities.c 
              identities.h 
              introspect.c 
              introspect.h 
              json.c 
              json.h 
              logger.c 
              logger.h 
              pam.c 
              parser.c 
              parser.h 
              strings.c
              strings.h )

# Captain! You will need to link against several system libraries. Translate this build system
# to alpine by the end of today.

# json-c
target_include_directories( pam_scitokens PUBLIC install/include )

target_link_libraries( pam_scitokens PUBLIC ${CMAKE_SOURCE_DIR}/install/lib/libjson-c.so )

# pam libraries
find_path(PAM_INCLUDE_DIR pam_appl.h PATH_SUFFIXES security)

find_library(PAM_LIBRARIES NAMES pam)

if (PAM_INCLUDE_DIR AND PAM_LIBRARIES)

  message(STATUS "Found PAM headers: ${PAM_INCLUDE_DIR}")

  message(STATUS "Found PAM: ${PAM_LIBRARIES}")

  set(PAM_FOUND TRUE CACHE INTERNAL "")

else()

  message(STATUS "PAM libraries not found")

  set(PAM_FOUND FALSE CACHE INTERNAL "")

endif()

target_include_directories( pam_scitokens PUBLIC ${PAM_INCLUDE_DIR} )

target_link_libraries( pam_scitokens PUBLIC ${PAM_LIBRARY} )

include( FindOpenSSL )

target_link_libraries( pam_scitokens PUBLIC ${OPENSSL_LIBRARIES} )

target_include_directories( pam_scitokens PUBLIC ${OPENSSL_INCLUDE_DIR} )

# curl
include( FindCURL )

target_link_libraries( pam_scitokens PUBLIC ${CURL_LIBRARIES} )

target_include_directories( pam_scitokens PUBLIC ${CURL_INCLUDE_DIRS} )
