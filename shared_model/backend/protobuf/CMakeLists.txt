#
# Copyright Soramitsu Co., Ltd. All Rights Reserved.
# SPDX-License-Identifier: Apache-2.0
#

add_library(shared_model_proto_backend
    impl/block.cpp
    impl/proposal.cpp
    impl/permissions.cpp
    impl/proto_block_factory.cpp
    impl/proto_block_json_converter.cpp
    impl/proto_query_response_factory.cpp
    impl/proto_tx_status_factory.cpp
    impl/proto_permission_to_string.cpp
    impl/transaction.cpp
    commands/impl/proto_add_asset_quantity.cpp
    commands/impl/proto_add_peer.cpp
    commands/impl/proto_add_signatory.cpp
    commands/impl/proto_append_role.cpp
    commands/impl/proto_command.cpp
    commands/impl/proto_create_account.cpp
    commands/impl/proto_create_asset.cpp
    commands/impl/proto_create_domain.cpp
    commands/impl/proto_create_role.cpp
    commands/impl/proto_detach_role.cpp
    commands/impl/proto_grant_permission.cpp
    commands/impl/proto_remove_signatory.cpp
    commands/impl/proto_revoke_permission.cpp
    commands/impl/proto_set_account_detail.cpp
    commands/impl/proto_set_quorum.cpp
    commands/impl/proto_subtract_asset_quantity.cpp
    commands/impl/proto_transfer_asset.cpp
    queries/impl/proto_query.cpp
    queries/impl/proto_get_account.cpp
    queries/impl/proto_get_account_asset_transactions.cpp
    queries/impl/proto_get_account_assets.cpp
    queries/impl/proto_get_account_detail.cpp
    queries/impl/proto_get_account_transactions.cpp
    queries/impl/proto_get_asset_info.cpp
    queries/impl/proto_get_block.cpp
    queries/impl/proto_get_role_permissions.cpp
    queries/impl/proto_get_roles.cpp
    queries/impl/proto_get_signatories.cpp
    queries/impl/proto_get_transactions.cpp
    queries/impl/proto_get_pending_transactions.cpp
    queries/impl/proto_blocks_query.cpp
    queries/impl/proto_query_payload_meta.cpp
    queries/impl/proto_tx_pagination_meta.cpp
    queries/impl/proto_asset_pagination_meta.cpp
    queries/impl/proto_account_detail_pagination_meta.cpp
    queries/impl/proto_account_detail_record_id.cpp
    queries/impl/proto_get_peers.cpp
    )

if (IROHA_ROOT_PROJECT)
  target_sources(shared_model_proto_backend PRIVATE
      query_responses/impl/proto_account_asset_response.cpp
      query_responses/impl/proto_account_detail_response.cpp
      query_responses/impl/proto_account_response.cpp
      query_responses/impl/proto_asset_response.cpp
      query_responses/impl/proto_error_query_response.cpp
      query_responses/impl/proto_query_response.cpp
      query_responses/impl/proto_role_permissions_response.cpp
      query_responses/impl/proto_roles_response.cpp
      query_responses/impl/proto_signatories_response.cpp
      query_responses/impl/proto_transaction_response.cpp
      query_responses/impl/proto_transaction_page_response.cpp
      query_responses/impl/proto_pending_transactions_page_response.cpp
      query_responses/impl/proto_block_query_response.cpp
      query_responses/impl/proto_block_response.cpp
      query_responses/impl/proto_block_error_response.cpp
      query_responses/impl/proto_get_block_response.cpp
      query_responses/impl/proto_peers_response.cpp
      transaction_responses/impl/proto_tx_response.cpp
      )
endif ()

target_link_libraries(shared_model_proto_backend
    schema
    common
    shared_model_interfaces
    logger
    )
