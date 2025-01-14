install_vineyard_headers("${CMAKE_CURRENT_SOURCE_DIR}")

# build vineyard-migrate
add_executable(vineyard-migrate "vineyard_migrate.cc")
target_link_libraries(vineyard-migrate vineyard_client
                                       ${ARROW_SHARED_LIB}
                                       ${Boost_LIBRARIES}
                                       ${GLOG_LIBRARIES}
                                       ${GFLAGS_LIBRARIES}
)
install_vineyard_target(vineyard-migrate)

# build vineyard-migrate-stream
add_executable(vineyard-migrate-stream "vineyard_migrate_stream.cc")
target_link_libraries(vineyard-migrate-stream vineyard_client
                                              ${ARROW_SHARED_LIB}
                                              ${Boost_LIBRARIES}
                                              ${GLOG_LIBRARIES}
                                              ${GFLAGS_LIBRARIES}
)
install_vineyard_target(vineyard-migrate-stream)

# install vineyard-migrate-to-local script.
install(PROGRAMS "${CMAKE_CURRENT_SOURCE_DIR}/vineyard-migrate-to-local.py"
        DESTINATION bin
        RENAME vineyard-migrate-to-local
)

add_custom_target(vineyard_migrate_python
        ALL
        COMMAND cp "$<TARGET_FILE:vineyard-migrate>" "${PROJECT_SOURCE_DIR}/modules/migrate/python/drivers/migrate/tools/"
        COMMAND cp "$<TARGET_FILE:vineyard-migrate-stream>" "${PROJECT_SOURCE_DIR}/modules/migrate/python/drivers/migrate/tools/"
        COMMAND cp "${CMAKE_CURRENT_SOURCE_DIR}/vineyard-migrate-to-local.py" "${PROJECT_SOURCE_DIR}/modules/migrate/python/drivers/migrate/tools/vineyard-migrate-to-local"
        DEPENDS vineyard-migrate
                vineyard-migrate-stream
        COMMENT "Copying the migration artifacts to the python package."
        VERBATIM)
