if (( ${rvm_ignore_rvmrc:=0} == 0 ))
  then
    [[ -n "${rvm_stored_umask:-}" ]] || export rvm_stored_umask=$(umask)

    rvm_rvmrc_files=("/etc/rvmrc" "$HOME/.rvmrc")
    if [[ -n "${rvm_prefix:-}" ]] && ! [[ "$HOME/.rvmrc" -ef "${rvm_prefix}/.rvmrc" ]]
    then rvm_rvmrc_files+=( "${rvm_prefix}/.rvmrc" )
    fi
    for rvmrc in "${rvm_rvmrc_files[@]}"
    do
      if [[ -f "$rvmrc" ]]
      then
        if GREP_OPTIONS="" \command \grep '^\s*rvm .*$' "$rvmrc" >/dev/null 2>&1
        then
          printf "%b" "
Error:
          $rvmrc is for rvm settings only.
          rvm CLI may NOT be called from within $rvmrc.
          Skipping the loading of $rvmrc"
          return 1
        else
          source "$rvmrc"
        fi
      fi
    done
    unset rvm_rvmrc_files
  fi

  # duplication marker jdgkjnfnkjdngjkfnd4fd
  export rvm_path
  if [[ -z "${rvm_path:-}" ]]
  then
    if [[ -n "${BASH_SOURCE:-$_}" && -f "${BASH_SOURCE:-$_}" ]]
    then
      rvm_path="${BASH_SOURCE:-$_}"
      rvm_path="$( \command \cd "${rvm_path%/scripts/initialize}">/dev/null; pwd )"
    elif (( UID == 0 )) && [[ -d "/usr/local/rvm" ]]
    then rvm_path="/usr/local/rvm"
    elif [[ -d "${HOME}/.rvm" ]]
    then rvm_path="${HOME}/.rvm"
    elif [[ -d "/usr/local/rvm" ]]
    then rvm_path="/usr/local/rvm"
    else echo "Can't find rvm install!" 1>&2 ; exit 1
    fi
  fi

  true ${rvm_scripts_path:="$rvm_path/scripts"}

  #
  # Setup RVM Environment variables.  Each RVM internal path is intended to be
  # configurable by the end users for whatever their needs may be.
  # They should be set in /etc/rvmrc and then $HOME/.rvmrc
  #
  # duplication marker kkdfkgnjfndgjkndfjkgnkfjdgn
  [[ -n "${rvm_user_install_flag:-}" ]] ||
  case "$rvm_path" in
    (/usr/local/rvm)         rvm_user_install_flag=0 ;;
    ($HOME/*|/${USER// /_}*) rvm_user_install_flag=1 ;;
    (*)                      rvm_user_install_flag=0 ;;
  esac
  export rvm_user_install_flag

  : \
    rvm_bin_path:${rvm_bin_path:="$rvm_path/bin"} \
    rvm_man_path:${rvm_man_path:="$rvm_path/share/man"} \
    rvm_archives_path:${rvm_archives_path:="$rvm_path/archives"} \
    rvm_docs_path:${rvm_docs_path:="$rvm_path/docs"} \
    rvm_environments_path:${rvm_environments_path:="$rvm_path/environments"} \
    rvm_examples_path:${rvm_examples_path:="$rvm_path/examples"} \
    rvm_gems_path:${rvm_gems_path:="$rvm_path/gems"} \
    rvm_gemsets_path:${rvm_gemsets_path:="$rvm_path/gemsets"} \
    rvm_help_path:${rvm_help_path:="$rvm_path/help"} \
    rvm_docs_path:${rvm_docs_path:="$rvm_path/docs"} \
    rvm_hooks_path:${rvm_hooks_path:="$rvm_path/hooks"} \
    rvm_lib_path:${rvm_lib_path:="$rvm_path/lib"} \
    rvm_log_path:${rvm_log_path:="$rvm_path/log"} \
    rvm_patches_path:${rvm_patches_path:="$rvm_path/patches"} \
    rvm_repos_path:${rvm_repos_path:="$rvm_path/repos"} \
    rvm_rubies_path:${rvm_rubies_path:="$rvm_path/rubies"} \
    rvm_rubygems_path:${rvm_rubygems_path:="$rvm_path/rubygems"} \
    rvm_src_path:${rvm_src_path:="$rvm_path/src"} \
    rvm_tmp_path:${rvm_tmp_path:="$rvm_path/tmp"} \
    rvm_user_path:${rvm_user_path:="$rvm_path/user"} \
    rvm_usr_path:${rvm_usr_path:="$rvm_path/usr"} \
    rvm_wrappers_path:${rvm_wrappers_path:="$rvm_path/wrappers"} \
    rvm_verbose_flag:${rvm_verbose_flag:=0} \
    rvm_debug_flag:${rvm_debug_flag:=0} \
    rvm_gems_cache_path:${rvm_gems_cache_path:=${rvm_gems_path:-"$rvm_path/gems"}/cache}

  export rvm_action rvm_alias_expanded rvm_archive_extension rvm_archives_path rvm_bin_flag rvm_bin_path rvm_debug_flag rvm_default_flag rvm_delete_flag rvm_docs_path rvm_docs_type rvm_dump_environment_flag rvm_environments_path rvm_error_message rvm_examples_path rvm_expanding_aliases rvm_file_name rvm_gemdir_flag rvm_gems_cache_path rvm_gems_path rvm_gemset_name rvm_gemset_separator rvm_gemsets_path rvm_gemstone_package_file rvm_gemstone_url rvm_head_flag rvm_help_path rvm_docs_path rvm_hook rvm_hooks_path rvm_install_on_use_flag rvm_lib_path rvm_llvm_flag rvm_loaded_flag rvm_log_path rvm_niceness rvm_nightly_flag rvm_only_path_flag rvm_parse_break rvm_patch_original_pwd rvm_patches_path rvm_path rvm_pretty_print_flag rvm_proxy rvm_quiet_flag rvm_reload_flag rvm_remove_flag rvm_repos_path rvm_rubies_path rvm_ruby_alias rvm_ruby_args rvm_ruby_binary rvm_ruby_bits rvm_ruby_configure rvm_ruby_file rvm_ruby_gem_home rvm_ruby_gem_path rvm_ruby_global_gems_path rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_major_version rvm_ruby_make rvm_ruby_make_install rvm_ruby_minor_version rvm_ruby_mode rvm_ruby_name rvm_ruby_package_file rvm_ruby_package_name rvm_ruby_patch rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_revision rvm_ruby_selected_flag rvm_ruby_sha rvm_ruby_string rvm_ruby_strings rvm_ruby_tag rvm_ruby_url rvm_ruby_user_tag rvm_ruby_version rvm_script_name rvm_scripts_path rvm_sdk rvm_user_install_flag rvm_silent_flag rvm_src_path rvm_sticky_flag rvm_system_flag rvm_tmp_path rvm_token rvm_trace_flag rvm_use_flag rvm_user_flag rvm_user_path rvm_usr_path rvm_verbose_flag rvm_wrapper_name rvm_wrappers_path rvm_ruby_repo_branch rvm_man_path rvm_remote_flag

fi # if [[ -z "${rvm_tmp_path:-}" ]] || (( __rvm_env_loaded == 1 ))
+++++ [[ -z '' ]]
+++++ typeset -f __rvm_cleanse_variables
+++++ __rvm_cleanse_variables
+++++ __rvm_unset_ruby_variables
+++++ unset rvm_env_string rvm_ruby_string rvm_ruby_strings rvm_ruby_binary rvm_ruby_gem_home rvm_ruby_gem_path rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_log_path rvm_ruby_major_version rvm_ruby_minor_version rvm_ruby_package_name rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_repo_branch rvm_ruby_revision rvm_ruby_selected_flag rvm_ruby_tag rvm_ruby_version rvm_head_flag rvm_ruby_package_file rvm_ruby_configure rvm_ruby_name rvm_ruby_url rvm_ruby_global_gems_path rvm_ruby_args rvm_ruby_name rvm_llvm_flag
+++++ __rvm_load_rvmrc
+++++ typeset _file
+++++ typeset -a rvm_rvmrc_files
+++++ ((  0 == 1  ))
+++++ [[ -n 0022 ]]
+++++ rvm_rvmrc_files=("/etc/rvmrc" "$HOME/.rvmrc")
+++++ [[ -n /Users/spencer.rohan ]]
+++++ [[ /Users/spencer.rohan/.rvmrc -ef /Users/spencer.rohan/.rvmrc ]]
+++++ rvm_rvmrc_files+=("${rvm_prefix}/.rvmrc")
+++++ for _file in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -s /etc/rvmrc ]]
+++++ for _file in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -s /Users/spencer.rohan/.rvmrc ]]
+++++ for _file in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -s /Users/spencer.rohan/.rvmrc ]]
+++++ return 0
+++++ [[ 0 -eq 1 ]]
+++++ unset rvm_gemset_name
+++++ unset rvm_configure_flags rvm_patch_names rvm_make_flags
+++++ unset rvm_env_string rvm_ruby_string rvm_action rvm_error_message rvm_force_flag rvm_debug_flag rvm_delete_flag rvm_summary_flag rvm_json_flag rvm_yaml_flag rvm_file_name rvm_user_flag rvm_system_flag rvm_install_flag rvm_llvm_flag rvm_sticky_flag rvm_rvmrc_flag rvm_gems_flag rvm_docs_flag rvm_ruby_alias rvm_static_flag rvm_archive_extension rvm_hook rvm_ruby_name rvm_remote_flag
+++++ __rvm_load_rvmrc
+++++ typeset _file
+++++ typeset -a rvm_rvmrc_files
+++++ ((  0 == 1  ))
+++++ [[ -n 0022 ]]
+++++ rvm_rvmrc_files=("/etc/rvmrc" "$HOME/.rvmrc")
+++++ [[ -n /Users/spencer.rohan ]]
+++++ [[ /Users/spencer.rohan/.rvmrc -ef /Users/spencer.rohan/.rvmrc ]]
+++++ rvm_rvmrc_files+=("${rvm_prefix}/.rvmrc")
+++++ for _file in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -s /etc/rvmrc ]]
+++++ for _file in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -s /Users/spencer.rohan/.rvmrc ]]
+++++ for _file in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -s /Users/spencer.rohan/.rvmrc ]]
+++++ return 0
+++++ ((  0 == 0  ))
+++++ [[ -n 0022 ]]
+++++ rvm_rvmrc_files=("/etc/rvmrc" "$HOME/.rvmrc")
+++++ [[ -n /Users/spencer.rohan ]]
+++++ [[ /Users/spencer.rohan/.rvmrc -ef /Users/spencer.rohan/.rvmrc ]]
+++++ rvm_rvmrc_files+=("${rvm_prefix}/.rvmrc")
+++++ for rvmrc in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -f /etc/rvmrc ]]
+++++ for rvmrc in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -f /Users/spencer.rohan/.rvmrc ]]
+++++ for rvmrc in '"${rvm_rvmrc_files[@]}"'
+++++ [[ -f /Users/spencer.rohan/.rvmrc ]]
+++++ unset rvm_rvmrc_files
+++++ export rvm_path
+++++ [[ -z /Users/spencer.rohan/.rvm ]]
+++++ true /Users/spencer.rohan/.rvm/scripts
+++++ [[ -n 1 ]]
+++++ export rvm_user_install_flag
+++++ : rvm_bin_path:/Users/spencer.rohan/.rvm/bin rvm_man_path:/Users/spencer.rohan/.rvm/share/man rvm_archives_path:/Users/spencer.rohan/.rvm/archives rvm_docs_path:/Users/spencer.rohan/.rvm/docs rvm_environments_path:/Users/spencer.rohan/.rvm/environments rvm_examples_path:/Users/spencer.rohan/.rvm/examples rvm_gems_path:/Users/spencer.rohan/.rvm/gems rvm_gemsets_path:/Users/spencer.rohan/.rvm/gemsets rvm_help_path:/Users/spencer.rohan/.rvm/help rvm_docs_path:/Users/spencer.rohan/.rvm/docs rvm_hooks_path:/Users/spencer.rohan/.rvm/hooks rvm_lib_path:/Users/spencer.rohan/.rvm/lib rvm_log_path:/Users/spencer.rohan/.rvm/log rvm_patches_path:/Users/spencer.rohan/.rvm/patches rvm_repos_path:/Users/spencer.rohan/.rvm/repos rvm_rubies_path:/Users/spencer.rohan/.rvm/rubies rvm_rubygems_path:/Users/spencer.rohan/.rvm/rubygems rvm_src_path:/Users/spencer.rohan/.rvm/src rvm_tmp_path:/Users/spencer.rohan/.rvm/tmp rvm_user_path:/Users/spencer.rohan/.rvm/user rvm_usr_path:/Users/spencer.rohan/.rvm/usr rvm_wrappers_path:/Users/spencer.rohan/.rvm/wrappers rvm_verbose_flag:0 rvm_debug_flag:0 rvm_gems_cache_path:/Users/spencer.rohan/.rvm/gems/cache
+++++ export rvm_action rvm_alias_expanded rvm_archive_extension rvm_archives_path rvm_bin_flag rvm_bin_path rvm_debug_flag rvm_default_flag rvm_delete_flag rvm_docs_path rvm_docs_type rvm_dump_environment_flag rvm_environments_path rvm_error_message rvm_examples_path rvm_expanding_aliases rvm_file_name rvm_gemdir_flag rvm_gems_cache_path rvm_gems_path rvm_gemset_name rvm_gemset_separator rvm_gemsets_path rvm_gemstone_package_file rvm_gemstone_url rvm_head_flag rvm_help_path rvm_docs_path rvm_hook rvm_hooks_path rvm_install_on_use_flag rvm_lib_path rvm_llvm_flag rvm_loaded_flag rvm_log_path rvm_niceness rvm_nightly_flag rvm_only_path_flag rvm_parse_break rvm_patch_original_pwd rvm_patches_path rvm_path rvm_pretty_print_flag rvm_proxy rvm_quiet_flag rvm_reload_flag rvm_remove_flag rvm_repos_path rvm_rubies_path rvm_ruby_alias rvm_ruby_args rvm_ruby_binary rvm_ruby_bits rvm_ruby_configure rvm_ruby_file rvm_ruby_gem_home rvm_ruby_gem_path rvm_ruby_global_gems_path rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_major_version rvm_ruby_make rvm_ruby_make_install rvm_ruby_minor_version rvm_ruby_mode rvm_ruby_name rvm_ruby_package_file rvm_ruby_package_name rvm_ruby_patch rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_revision rvm_ruby_selected_flag rvm_ruby_sha rvm_ruby_string rvm_ruby_strings rvm_ruby_tag rvm_ruby_url rvm_ruby_user_tag rvm_ruby_version rvm_script_name rvm_scripts_path rvm_sdk rvm_user_install_flag rvm_silent_flag rvm_src_path rvm_sticky_flag rvm_system_flag rvm_tmp_path rvm_token rvm_trace_flag rvm_use_flag rvm_user_flag rvm_user_path rvm_usr_path rvm_verbose_flag rvm_wrapper_name rvm_wrappers_path rvm_ruby_repo_branch rvm_man_path rvm_remote_flag
++++ __rvm_setup
++++ __variables_definition export
++++ typeset -a __variables_list __array_list
++++ typeset __method
++++ __method=export
++++ __variables_list=(rvm_head_flag rvm_ruby_selected_flag rvm_user_install_flag rvm_path_flag rvm_cron_flag rvm_static_flag rvm_default_flag rvm_loaded_flag rvm_llvm_flag rvm_skip_autoreconf_flag rvm_dynamic_extensions_flag rvm_18_flag rvm_19_flag rvm_20_flag rvm_21_flag rvm_force_autoconf_flag rvm_dump_environment_flag rvm_curl_flags rvm_rubygems_version rvm_verbose_flag rvm_debug_flag rvm_trace_flag __array_start rvm_skip_pristine_flag rvm_pretty_print_flag rvm_create_flag rvm_remove_flag rvm_movable_flag rvm_archive_flag rvm_gemdir_flag rvm_reload_flag rvm_auto_reload_flag rvm_disable_binary_flag rvm_ignore_gemsets_flag rvm_skip_gemsets_flag rvm_install_on_use_flag rvm_remote_flag rvm_verify_downloads_flag rvm_skip_openssl_flag rvm_gems_cache_path rvm_gems_path rvm_man_path rvm_ruby_gem_path rvm_ruby_log_path rvm_gems_cache_path rvm_archives_path rvm_docs_path rvm_environments_path rvm_examples_path rvm_gems_path rvm_gemsets_path rvm_help_path rvm_hooks_path rvm_lib_path rvm_log_path rvm_patches_path rvm_repos_path rvm_rubies_path rvm_scripts_path rvm_src_path rvm_tmp_path rvm_user_path rvm_usr_path rvm_wrappers_path rvm_stored_errexit rvm_ruby_strings rvm_ruby_binary rvm_ruby_gem_home rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_major_version rvm_ruby_minor_version rvm_ruby_package_name rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_repo_branch rvm_ruby_revision rvm_ruby_tag rvm_ruby_sha rvm_ruby_version rvm_ruby_package_file rvm_ruby_name rvm_ruby_name rvm_ruby_args rvm_ruby_user_tag rvm_ruby_patch detected_rvm_ruby_name __rvm_env_loaded next_token rvm_error_message rvm_gemset_name rvm_parse_break rvm_token rvm_action rvm_export_args rvm_gemset_separator rvm_expanding_aliases rvm_tar_command rvm_tar_options rvm_patch_original_pwd rvm_project_rvmrc rvm_archive_extension rvm_autoinstall_bundler_flag rvm_codesign_identity rvm_expected_gemset_name rvm_without_gems rvm_with_gems rvm_with_default_gems rvm_ignore_dotfiles_flag rvm_fuzzy_flag rvm_autolibs_flag rvm_autolibs_flag_number rvm_autolibs_flag_runner rvm_quiet_curl_flag rvm_max_time_flag rvm_error_clr rvm_warn_clr rvm_debug_clr rvm_notify_clr rvm_reset_clr rvm_error_color rvm_warn_color rvm_debug_color rvm_notify_color rvm_reset_color rvm_log_timestamp rvm_log_filesystem rvm_log_namelen rvm_show_log_lines_on_error)
++++ __array_list=(rvm_patch_names rvm_ree_options rvm_autoconf_flags rvm_architectures)
++++ case "${__method}" in
++++ true
++++ export rvm_head_flag rvm_ruby_selected_flag rvm_user_install_flag rvm_path_flag rvm_cron_flag rvm_static_flag rvm_default_flag rvm_loaded_flag rvm_llvm_flag rvm_skip_autoreconf_flag rvm_dynamic_extensions_flag rvm_18_flag rvm_19_flag rvm_20_flag rvm_21_flag rvm_force_autoconf_flag rvm_dump_environment_flag rvm_curl_flags rvm_rubygems_version rvm_verbose_flag rvm_debug_flag rvm_trace_flag __array_start rvm_skip_pristine_flag rvm_pretty_print_flag rvm_create_flag rvm_remove_flag rvm_movable_flag rvm_archive_flag rvm_gemdir_flag rvm_reload_flag rvm_auto_reload_flag rvm_disable_binary_flag rvm_ignore_gemsets_flag rvm_skip_gemsets_flag rvm_install_on_use_flag rvm_remote_flag rvm_verify_downloads_flag rvm_skip_openssl_flag rvm_gems_cache_path rvm_gems_path rvm_man_path rvm_ruby_gem_path rvm_ruby_log_path rvm_gems_cache_path rvm_archives_path rvm_docs_path rvm_environments_path rvm_examples_path rvm_gems_path rvm_gemsets_path rvm_help_path rvm_hooks_path rvm_lib_path rvm_log_path rvm_patches_path rvm_repos_path rvm_rubies_path rvm_scripts_path rvm_src_path rvm_tmp_path rvm_user_path rvm_usr_path rvm_wrappers_path rvm_stored_errexit rvm_ruby_strings rvm_ruby_binary rvm_ruby_gem_home rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_major_version rvm_ruby_minor_version rvm_ruby_package_name rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_repo_branch rvm_ruby_revision rvm_ruby_tag rvm_ruby_sha rvm_ruby_version rvm_ruby_package_file rvm_ruby_name rvm_ruby_name rvm_ruby_args rvm_ruby_user_tag rvm_ruby_patch detected_rvm_ruby_name __rvm_env_loaded next_token rvm_error_message rvm_gemset_name rvm_parse_break rvm_token rvm_action rvm_export_args rvm_gemset_separator rvm_expanding_aliases rvm_tar_command rvm_tar_options rvm_patch_original_pwd rvm_project_rvmrc rvm_archive_extension rvm_autoinstall_bundler_flag rvm_codesign_identity rvm_expected_gemset_name rvm_without_gems rvm_with_gems rvm_with_default_gems rvm_ignore_dotfiles_flag rvm_fuzzy_flag rvm_autolibs_flag rvm_autolibs_flag_number rvm_autolibs_flag_runner rvm_quiet_curl_flag rvm_max_time_flag rvm_error_clr rvm_warn_clr rvm_debug_clr rvm_notify_clr rvm_reset_clr rvm_error_color rvm_warn_color rvm_debug_color rvm_notify_color rvm_reset_color rvm_log_timestamp rvm_log_filesystem rvm_log_namelen rvm_show_log_lines_on_error
++++ [[ -n 3.2.57(1)-release ]]
++++ export -fn __rvm_select_version_variables __rvm_ruby_string_parse_ __rvm_rm_rf_verbose __rvm_parse_args
++++ ((  __rvm_env_loaded != 1  ))
++++ [[ -n 3.2.57(1)-release ]]
++++ __function_on_stack cd pushd popd
++++ __rvm_string_includes '__function_on_stack __rvm_setup __rvm_do_with_env_before __rvm_cd_functions_set __zsh_like_cd cd source source' cd pushd popd
++++ typeset __search '__text=__function_on_stack __rvm_setup __rvm_do_with_env_before __rvm_cd_functions_set __zsh_like_cd cd source source'
++++ shift
++++ for __search in '"$@"'
++++ [[  __function_on_stack __rvm_setup __rvm_do_with_env_before __rvm_cd_functions_set __zsh_like_cd cd source source  == *\ \c\d\ * ]]
++++ return 0
++++ [[ -n '' ]]
++++ [[ -n '' ]]
++++ __rvm_project_rvmrc
++++ export __rvm_project_rvmrc_lock
++++ : __rvm_project_rvmrc_lock:0
++++ : __rvm_project_rvmrc_lock:1
++++ ((  __rvm_project_rvmrc_lock > 1  ))
++++ typeset working_dir found_file rvm_trustworthiness_result save_PATH
++++ working_dir=/Users/spencer.rohan/oh-my-git
++++ save_PATH=/bin:/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/local/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0@global/bin:/Users/spencer.rohan/.rvm/rubies/ruby-2.3.0/bin:/bin:/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/spencer.rohan/.rvm/bin
++++ :
++++ [[ -z /Users/spencer.rohan/oh-my-git ]]
++++ [[ /Users/spencer.rohan == \/\U\s\e\r\s\/\s\p\e\n\c\e\r\.\r\o\h\a\n\/\o\h\-\m\y\-\g\i\t ]]
++++ [[ /Users/spencer.rohan == \/\U\s\e\r\s\/\s\p\e\n\c\e\r\.\r\o\h\a\n\/\o\h\-\m\y\-\g\i\t ]]
++++ __rvm_project_dir_check /Users/spencer.rohan/oh-my-git found_file
++++ typeset _found_file path_to_check variable variable_default
++++ typeset -a _valid_files
++++ path_to_check=/Users/spencer.rohan/oh-my-git
++++ variable=found_file
++++ variable_default=
++++ _valid_files=("$path_to_check" "$path_to_check/.rvmrc" "$path_to_check/.versions.conf" "$path_to_check/.ruby-version" "$path_to_check/.rbfu-version" "$path_to_check/.rbenv-version" "$path_to_check/Gemfile")
++++ __rvm_find_first_file _found_file /Users/spencer.rohan/oh-my-git /Users/spencer.rohan/oh-my-git/.rvmrc /Users/spencer.rohan/oh-my-git/.versions.conf /Users/spencer.rohan/oh-my-git/.ruby-version /Users/spencer.rohan/oh-my-git/.rbfu-version /Users/spencer.rohan/oh-my-git/.rbenv-version /Users/spencer.rohan/oh-my-git/Gemfile
++++ typeset _first_file _variable_first_file __file_enum
++++ _first_file=
++++ _variable_first_file=_found_file
++++ shift
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git ]]
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git/.rvmrc ]]
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git/.versions.conf ]]
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git/.ruby-version ]]
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git/.rbfu-version ]]
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git/.rbenv-version ]]
++++ for __file_enum in '"$@"'
++++ [[ -f /Users/spencer.rohan/oh-my-git/Gemfile ]]
++++ eval '_found_file=""'
_found_file=""
+++++ _found_file=
++++ return 1
++++ true
++++ [[ '' == \G\e\m\f\i\l\e ]]
++++ [[ '' == \/\U\s\e\r\s\/\s\p\e\n\c\e\r\.\r\o\h\a\n\/\.\r\v\m\r\c ]]
++++ [[ -n found_file ]]
++++ eval 'found_file="${_found_file:-}"'
found_file="${_found_file:-}"
+++++ found_file=
++++ [[ -n '' ]]
++++ typeset __result=1
++++ unset RVM_PROJECT_PATH
++++ return 1
++++ working_dir=/Users/spencer.rohan
++++ :
++++ [[ -z /Users/spencer.rohan ]]
++++ [[ /Users/spencer.rohan == \/\U\s\e\r\s\/\s\p\e\n\c\e\r\.\r\o\h\a\n ]]
++++ ((  0 >= 1  ))
++++ [[ -n '' ]]
++++ __rvm_project_ruby_env_check_unload
++++ ((  0 > 0  ))
++++ rvm_saved_env=()
++++ unset rvm_current_rvmrc rvm_previous_environment
++++ break
++++ unset __rvm_project_rvmrc_lock
++++ return 1
++++ true
++++ __rvm_after_cd
++++ typeset rvm_hook
++++ rvm_hook=after_cd
++++ [[ -n /Users/spencer.rohan/.rvm/scripts ]]
++++ source /Users/spencer.rohan/.rvm/scripts/hook
#!/usr/bin/env bash

# need additional step to not redefine variables value if already set
\typeset old_rvm_verbose_flag old_rvm_debug_flag >/dev/null 2>/dev/null
+++++ typeset old_rvm_verbose_flag old_rvm_debug_flag
old_rvm_verbose_flag=${rvm_verbose_flag:-0}
+++++ old_rvm_verbose_flag=0
old_rvm_debug_flag=${rvm_debug_flag:-0}
+++++ old_rvm_debug_flag=0

# silence Zsh redefinitions
\typeset rvm_verbose_flag rvm_debug_flag hooks >/dev/null 2>/dev/null
+++++ typeset rvm_verbose_flag rvm_debug_flag hooks

true rvm_verbose_flag:${rvm_verbose_flag:=${old_rvm_verbose_flag}} \
  rvm_debug_flag:${rvm_debug_flag:=${old_rvm_debug_flag}} rvm_hook:${rvm_hook:=}
+++++ true rvm_verbose_flag:0 rvm_debug_flag:0 rvm_hook:after_cd

if
  [[ -n "$rvm_hook" ]]
then
  hooks=()
  if
    [[ -d "$rvm_hooks_path" ]]
  then
    hooks+=( "$rvm_hooks_path" )
  fi
  if
    [[ "$PWD/.rvm/hooks" != "$rvm_hooks_path" && -d "$PWD/.rvm/hooks" ]]
  then
    hooks+=( "$PWD/.rvm/hooks" )
  fi

  if
    [[ -n "${hooks[*]}" ]]
  then
    _hooks_list=($(
      __rvm_find -L "${hooks[@]}" -iname "$rvm_hook*" -type f 2>/dev/null || true
    ))

    for hook in "${_hooks_list[@]}"
    do
      if [[ -x "${hook}" || "${hook##*/}" == "$rvm_hook" ]]
      then
        rvm_debug "Running $hook"
        __rvm_conditionally_do_with_env . "${hook}" >&2
      fi
    done
  fi
fi
+++++ [[ -n after_cd ]]
+++++ hooks=()
+++++ [[ -d /Users/spencer.rohan/.rvm/hooks ]]
+++++ hooks+=("$rvm_hooks_path")
+++++ [[ /Users/spencer.rohan/oh-my-git/.rvm/hooks != \/\U\s\e\r\s\/\s\p\e\n\c\e\r\.\r\o\h\a\n\/\.\r\v\m\/\h\o\o\k\s ]]
+++++ [[ -d /Users/spencer.rohan/oh-my-git/.rvm/hooks ]]
+++++ [[ -n /Users/spencer.rohan/.rvm/hooks ]]
+++++ _hooks_list=($(
      __rvm_find -L "${hooks[@]}" -iname "$rvm_hook*" -type f 2>/dev/null || true
    ))

      __rvm_find -L "${hooks[@]}" -iname "$rvm_hook*" -type f 2>/dev/null || true
++++++ __rvm_find -L /Users/spencer.rohan/.rvm/hooks -iname 'after_cd*' -type f

+++++ for hook in '"${_hooks_list[@]}"'
+++++ [[ -x /Users/spencer.rohan/.rvm/hooks/after_cd_bundler ]]
+++++ [[ after_cd_bundler == \a\f\t\e\r\_\c\d ]]
+++++ for hook in '"${_hooks_list[@]}"'
+++++ [[ -x /Users/spencer.rohan/.rvm/hooks/after_cd_install_vendored_gems ]]
+++++ [[ after_cd_install_vendored_gems == \a\f\t\e\r\_\c\d ]]

unset rvm_hook hooks _hooks_list hook
+++++ unset rvm_hook hooks _hooks_list hook
++++ __rvm_do_with_env_after
++++ __rvm_teardown
++++ builtin command -v __rvm_cleanup_tmp
++++ __rvm_cleanup_tmp
++++ [[ -d /Users/spencer.rohan/.rvm/tmp/ ]]
++++ case "${rvm_tmp_path%\/}" in
++++ __rvm_rm_rf '/Users/spencer.rohan/.rvm/tmp/1196*'
++++ __rvm_rm_rf_verbose '/Users/spencer.rohan/.rvm/tmp/1196*'
++++ typeset target
++++ target='/Users/spencer.rohan/.rvm/tmp/1196*'
++++ [[ -n '' ]]
++++ [[ -n 3.2.57(1)-release ]]
++++ builtin shopt -s extglob
++++ case "${target}" in
++++ [[ -z /Users/spencer.rohan/.rvm/tmp/1196* ]]
++++ [[ -d /Users/spencer.rohan/.rvm/tmp/1196* ]]
++++ [[ -f /Users/spencer.rohan/.rvm/tmp/1196* ]]
++++ [[ -L /Users/spencer.rohan/.rvm/tmp/1196* ]]
++++ rvm_debug '__rvm_rm_rf already gone: /Users/spencer.rohan/.rvm/tmp/1196*'
++++ ((  0  ))
++++ return 0
++++ true
++++ true
++++ export __rvm_env_loaded
++++ : __rvm_env_loaded:1:
++++ : __rvm_env_loaded:1:
++++ : __rvm_env_loaded:0:
++++ [[ -z /Users/spencer.rohan/.rvm/tmp ]]
++++ ((  __rvm_env_loaded > 0  ))
++++ [[ -n 3.2.57(1)-release ]]
++++ trap - EXIT HUP INT QUIT TERM
++++ [[ -n '' ]]
++++ [[ -n 0022 ]]
++++ umask 0022
++++ unset rvm_stored_umask
++++ builtin command -v __rvm_cleanup_download
++++ [[ '' == \1 ]]
++++ __variables_definition unset
++++ typeset -a __variables_list __array_list
++++ typeset __method
++++ __method=unset
++++ __variables_list=(rvm_head_flag rvm_ruby_selected_flag rvm_user_install_flag rvm_path_flag rvm_cron_flag rvm_static_flag rvm_default_flag rvm_loaded_flag rvm_llvm_flag rvm_skip_autoreconf_flag rvm_dynamic_extensions_flag rvm_18_flag rvm_19_flag rvm_20_flag rvm_21_flag rvm_force_autoconf_flag rvm_dump_environment_flag rvm_curl_flags rvm_rubygems_version rvm_verbose_flag rvm_debug_flag rvm_trace_flag __array_start rvm_skip_pristine_flag rvm_pretty_print_flag rvm_create_flag rvm_remove_flag rvm_movable_flag rvm_archive_flag rvm_gemdir_flag rvm_reload_flag rvm_auto_reload_flag rvm_disable_binary_flag rvm_ignore_gemsets_flag rvm_skip_gemsets_flag rvm_install_on_use_flag rvm_remote_flag rvm_verify_downloads_flag rvm_skip_openssl_flag rvm_gems_cache_path rvm_gems_path rvm_man_path rvm_ruby_gem_path rvm_ruby_log_path rvm_gems_cache_path rvm_archives_path rvm_docs_path rvm_environments_path rvm_examples_path rvm_gems_path rvm_gemsets_path rvm_help_path rvm_hooks_path rvm_lib_path rvm_log_path rvm_patches_path rvm_repos_path rvm_rubies_path rvm_scripts_path rvm_src_path rvm_tmp_path rvm_user_path rvm_usr_path rvm_wrappers_path rvm_stored_errexit rvm_ruby_strings rvm_ruby_binary rvm_ruby_gem_home rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_major_version rvm_ruby_minor_version rvm_ruby_package_name rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_repo_branch rvm_ruby_revision rvm_ruby_tag rvm_ruby_sha rvm_ruby_version rvm_ruby_package_file rvm_ruby_name rvm_ruby_name rvm_ruby_args rvm_ruby_user_tag rvm_ruby_patch detected_rvm_ruby_name __rvm_env_loaded next_token rvm_error_message rvm_gemset_name rvm_parse_break rvm_token rvm_action rvm_export_args rvm_gemset_separator rvm_expanding_aliases rvm_tar_command rvm_tar_options rvm_patch_original_pwd rvm_project_rvmrc rvm_archive_extension rvm_autoinstall_bundler_flag rvm_codesign_identity rvm_expected_gemset_name rvm_without_gems rvm_with_gems rvm_with_default_gems rvm_ignore_dotfiles_flag rvm_fuzzy_flag rvm_autolibs_flag rvm_autolibs_flag_number rvm_autolibs_flag_runner rvm_quiet_curl_flag rvm_max_time_flag rvm_error_clr rvm_warn_clr rvm_debug_clr rvm_notify_clr rvm_reset_clr rvm_error_color rvm_warn_color rvm_debug_color rvm_notify_color rvm_reset_color rvm_log_timestamp rvm_log_filesystem rvm_log_namelen rvm_show_log_lines_on_error)
++++ __array_list=(rvm_patch_names rvm_ree_options rvm_autoconf_flags rvm_architectures)
++++ case "${__method}" in
++++ unset rvm_patch_names rvm_ree_options rvm_autoconf_flags rvm_architectures
++++ unset rvm_head_flag rvm_ruby_selected_flag rvm_user_install_flag rvm_path_flag rvm_cron_flag rvm_static_flag rvm_default_flag rvm_loaded_flag rvm_llvm_flag rvm_skip_autoreconf_flag rvm_dynamic_extensions_flag rvm_18_flag rvm_19_flag rvm_20_flag rvm_21_flag rvm_force_autoconf_flag rvm_dump_environment_flag rvm_curl_flags rvm_rubygems_version rvm_verbose_flag rvm_debug_flag rvm_trace_flag __array_start rvm_skip_pristine_flag rvm_pretty_print_flag rvm_create_flag rvm_remove_flag rvm_movable_flag rvm_archive_flag rvm_gemdir_flag rvm_reload_flag rvm_auto_reload_flag rvm_disable_binary_flag rvm_ignore_gemsets_flag rvm_skip_gemsets_flag rvm_install_on_use_flag rvm_remote_flag rvm_verify_downloads_flag rvm_skip_openssl_flag rvm_gems_cache_path rvm_gems_path rvm_man_path rvm_ruby_gem_path rvm_ruby_log_path rvm_gems_cache_path rvm_archives_path rvm_docs_path rvm_environments_path rvm_examples_path rvm_gems_path rvm_gemsets_path rvm_help_path rvm_hooks_path rvm_lib_path rvm_log_path rvm_patches_path rvm_repos_path rvm_rubies_path rvm_scripts_path rvm_src_path rvm_tmp_path rvm_user_path rvm_usr_path rvm_wrappers_path rvm_stored_errexit rvm_ruby_strings rvm_ruby_binary rvm_ruby_gem_home rvm_ruby_home rvm_ruby_interpreter rvm_ruby_irbrc rvm_ruby_major_version rvm_ruby_minor_version rvm_ruby_package_name rvm_ruby_patch_level rvm_ruby_release_version rvm_ruby_repo_url rvm_ruby_repo_branch rvm_ruby_revision rvm_ruby_tag rvm_ruby_sha rvm_ruby_version rvm_ruby_package_file rvm_ruby_name rvm_ruby_name rvm_ruby_args rvm_ruby_user_tag rvm_ruby_patch detected_rvm_ruby_name __rvm_env_loaded next_token rvm_error_message rvm_gemset_name rvm_parse_break rvm_token rvm_action rvm_export_args rvm_gemset_separator rvm_expanding_aliases rvm_tar_command rvm_tar_options rvm_patch_original_pwd rvm_project_rvmrc rvm_archive_extension rvm_autoinstall_bundler_flag rvm_codesign_identity rvm_expected_gemset_name rvm_without_gems rvm_with_gems rvm_with_default_gems rvm_ignore_dotfiles_flag rvm_fuzzy_flag rvm_autolibs_flag rvm_autolibs_flag_number rvm_autolibs_flag_runner rvm_quiet_curl_flag rvm_max_time_flag rvm_error_clr rvm_warn_clr rvm_debug_clr rvm_notify_clr rvm_reset_clr rvm_error_color rvm_warn_color rvm_debug_color rvm_notify_color rvm_reset_color rvm_log_timestamp rvm_log_filesystem rvm_log_namelen rvm_show_log_lines_on_error
++++ [[ -n 3.2.57(1)-release ]]
++++ export -fn __rvm_select_version_variables __rvm_ruby_string_parse_ __rvm_rm_rf_verbose __rvm_parse_args
++++ return 0
++++ return 0
++++ true
++++ pwd
+++ DIR=/Users/spencer.rohan/oh-my-git
+++ source /Users/spencer.rohan/oh-my-git/base.sh
function enrich {
    local flag=$1
    local symbol=$2

    local color_on=${3:-$omg_default_color_on}

    if [[ $flag != true && $omg_use_color_off == false ]]; then symbol=' '; fi
    if [[ $flag == true ]]; then local color=$color_on; else local color=$omg_default_color_off; fi

    echo -n "${prompt}${color}${symbol}${reset} "
}

function get_current_action () {
    local info="$(git rev-parse --git-dir 2>/dev/null)"
    if [ -n "$info" ]; then
        local action
        if [ -f "$info/rebase-merge/interactive" ]
        then
            action=${is_rebasing_interactively:-"rebase -i"}
        elif [ -d "$info/rebase-merge" ]
        then
            action=${is_rebasing_merge:-"rebase -m"}
        else
            if [ -d "$info/rebase-apply" ]
            then
                if [ -f "$info/rebase-apply/rebasing" ]
                then
                    action=${is_rebasing:-"rebase"}
                elif [ -f "$info/rebase-apply/applying" ]
                then
                    action=${is_applying_mailbox_patches:-"am"}
                else
                    action=${is_rebasing_mailbox_patches:-"am/rebase"}
                fi
            elif [ -f "$info/MERGE_HEAD" ]
            then
                action=${is_merging:-"merge"}
            elif [ -f "$info/CHERRY_PICK_HEAD" ]
            then
                action=${is_cherry_picking:-"cherry-pick"}
            elif [ -f "$info/BISECT_LOG" ]
            then
                action=${is_bisecting:-"bisect"}
            fi
        fi

        if [[ -n $action ]]; then printf "%s" "${1-}$action${2-}"; fi
    fi
}

function build_prompt {
    local enabled=`git config --local --get oh-my-git.enabled`
    if [[ ${enabled} == false ]]; then
        echo "${PSORG}"
        exit;
    fi

    local prompt=""

    # Git info
    local current_commit_hash=$(git rev-parse HEAD 2> /dev/null)
    if [[ -n $current_commit_hash ]]; then local is_a_git_repo=true; fi

    if [[ $is_a_git_repo == true ]]; then
        local current_branch=$(git rev-parse --abbrev-ref HEAD 2> /dev/null)
        if [[ $current_branch == 'HEAD' ]]; then local detached=true; fi

        local number_of_logs="$(git log --pretty=oneline -n1 2> /dev/null | wc -l)"
        if [[ $number_of_logs -eq 0 ]]; then
            local just_init=true
        else
            local upstream=$(git rev-parse --symbolic-full-name --abbrev-ref @{upstream} 2> /dev/null)
            if [[ -n "${upstream}" && "${upstream}" != "@{upstream}" ]]; then local has_upstream=true; fi

            local git_status="$(git status --porcelain 2> /dev/null)"
            local action="$(get_current_action)"

            if [[ $git_status =~ ($'\n'|^).M ]]; then local has_modifications=true; fi
            if [[ $git_status =~ ($'\n'|^)M ]]; then local has_modifications_cached=true; fi
            if [[ $git_status =~ ($'\n'|^)A ]]; then local has_adds=true; fi
            if [[ $git_status =~ ($'\n'|^).D ]]; then local has_deletions=true; fi
            if [[ $git_status =~ ($'\n'|^)D ]]; then local has_deletions_cached=true; fi
            if [[ $git_status =~ ($'\n'|^)[MAD] && ! $git_status =~ ($'\n'|^).[MAD\?] ]]; then local ready_to_commit=true; fi

            local number_of_untracked_files=$(\grep -c "^??" <<< "${git_status}")
            if [[ $number_of_untracked_files -gt 0 ]]; then local has_untracked_files=true; fi

            local tag_at_current_commit=$(git describe --exact-match --tags $current_commit_hash 2> /dev/null)
            if [[ -n $tag_at_current_commit ]]; then local is_on_a_tag=true; fi

            if [[ $has_upstream == true ]]; then
                local commits_diff="$(git log --pretty=oneline --topo-order --left-right ${current_commit_hash}...${upstream} 2> /dev/null)"
                local commits_ahead=$(\grep -c "^<" <<< "$commits_diff")
                local commits_behind=$(\grep -c "^>" <<< "$commits_diff")
            fi

            if [[ $commits_ahead -gt 0 && $commits_behind -gt 0 ]]; then local has_diverged=true; fi
            if [[ $has_diverged == false && $commits_ahead -gt 0 ]]; then local should_push=true; fi

            local will_rebase=$(git config --get branch.${current_branch}.rebase 2> /dev/null)

            local number_of_stashes="$(git stash list -n1 2> /dev/null | wc -l)"
            if [[ $number_of_stashes -gt 0 ]]; then local has_stashes=true; fi
        fi
    fi

    echo "$(custom_build_prompt ${enabled:-true} ${current_commit_hash:-""} ${is_a_git_repo:-false} ${current_branch:-""} ${detached:-false} ${just_init:-false} ${has_upstream:-false} ${has_modifications:-false} ${has_modifications_cached:-false} ${has_adds:-false} ${has_deletions:-false} ${has_deletions_cached:-false} ${has_untracked_files:-false} ${ready_to_commit:-false} ${tag_at_current_commit:-""} ${is_on_a_tag:-false} ${has_upstream:-false} ${commits_ahead:-false} ${commits_behind:-false} ${has_diverged:-false} ${should_push:-false} ${will_rebase:-false} ${has_stashes:-false} ${action})"

}

function_exists() {
    declare -f -F $1 > /dev/null
    return $?
}

function eval_prompt_callback_if_present {
        function_exists omg_prompt_callback && echo "$(omg_prompt_callback)"
}
+++ : '\n\[\033[0;31m\]\W\[\033[0m\]\[\033[0m\]' $'�\207\231'
+++ : '\n\[\033[0;31m\]\W\[\033[0m\]\[\033[0m\]' $'�\207\231'
+++ : $'�\210\216'
+++ : $'�\205\227'
+++ : $'�\205�'
+++ : $'�\205�'
+++ : $'�\201'
+++ : $'�\230'
+++ : $'�\223'
+++ : $'�\206\237'
+++ : $'�\205\200'
+++ : $'�\204\211'
+++ : $'�\210�'
+++ : $'�\207�'
+++ : 
+++ : $'�\210\205'
+++ : $'�\213'
+++ : 
+++ : $'�\216'
+++ : $'�\204\235'
+++ : '\[\033[1;37m\]'
+++ : '\[\033[0m\]'
+++ : '\e[0;31m\e[40m'
+++ PROMPT='$(build_prompt)'
+++ RPROMPT='%{$reset_color%}%T %{$fg_bold[white]%} %n@%m%{$reset_color%}'
+++ PS2='→ '
+++ PROMPT_COMMAND=bash_prompt
export PATH=/usr/local/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0@global/bin:/Users/spencer.rohan/.rvm/rubies/ruby-2.3.0/bin:/bin:/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/spencer.rohan/.rvm/bin
++ export PATH=/usr/local/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0@global/bin:/Users/spencer.rohan/.rvm/rubies/ruby-2.3.0/bin:/bin:/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/spencer.rohan/.rvm/bin
++ PATH=/usr/local/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0/bin:/Users/spencer.rohan/.rvm/gems/ruby-2.3.0@global/bin:/Users/spencer.rohan/.rvm/rubies/ruby-2.3.0/bin:/bin:/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/spencer.rohan/.rvm/bin
bash_prompt
++ bash_prompt
build_prompt
+++ build_prompt
git config --local --get oh-my-git.enabled
++++ git config --local --get oh-my-git.enabled
+++ local enabled=
+++ [[ '' == false ]]
+++ local prompt=
git rev-parse HEAD 2> /dev/null
++++ git rev-parse HEAD
+++ local current_commit_hash=c6e6b1d3c9157294e5854825b00825047fdf2f5f
+++ [[ -n c6e6b1d3c9157294e5854825b00825047fdf2f5f ]]
+++ local is_a_git_repo=true
+++ [[ true == true ]]
git rev-parse --abbrev-ref HEAD 2> /dev/null
++++ git rev-parse --abbrev-ref HEAD
+++ local current_branch=feature/sandbox
+++ [[ feature/sandbox == \H\E\A\D ]]
git log --pretty=oneline -n1 2> /dev/null | wc -l
++++ git log --pretty=oneline -n1
++++ wc -l
+++ local 'number_of_logs=       1'
+++ [[        1 -eq 0 ]]
git rev-parse --symbolic-full-name --abbrev-ref @{upstream} 2> /dev/null
++++ git rev-parse --symbolic-full-name --abbrev-ref '@{upstream}'
+++ local upstream=
+++ [[ -n '' ]]
git status --porcelain 2> /dev/null
++++ git status --porcelain
+++ local 'git_status= M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php'
get_current_action
++++ get_current_action
git rev-parse --git-dir 2>/dev/null
+++++ git rev-parse --git-dir
++++ local info=.git
++++ '[' -n .git ']'
++++ local action
++++ '[' -f .git/rebase-merge/interactive ']'
++++ '[' -d .git/rebase-merge ']'
++++ '[' -d .git/rebase-apply ']'
++++ '[' -f .git/MERGE_HEAD ']'
++++ '[' -f .git/CHERRY_PICK_HEAD ']'
++++ '[' -f .git/BISECT_LOG ']'
++++ [[ -n '' ]]
+++ local action=
+++ [[  M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php =~ (
|^).M ]]
+++ local has_modifications=true
+++ [[  M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php =~ (
|^)M ]]
+++ [[  M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php =~ (
|^)A ]]
+++ [[  M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php =~ (
|^).D ]]
+++ local has_deletions=true
+++ [[  M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php =~ (
|^)D ]]
+++ [[  M controllers/controller.products.php
 M lib/Site.php
 M page.php
 M views/page.twig
 D widgets/widget.example.php
?? controllers/controller.retailers.php =~ (
|^)[MAD] ]]
\grep -c "^??" <<< "${git_status}"
++++ grep -c '^??'
+++ local number_of_untracked_files=1
+++ [[ 1 -gt 0 ]]
+++ local has_untracked_files=true
git describe --exact-match --tags $current_commit_hash 2> /dev/null
++++ git describe --exact-match --tags c6e6b1d3c9157294e5854825b00825047fdf2f5f
+++ local tag_at_current_commit=
+++ [[ -n '' ]]
+++ [[ '' == true ]]
+++ [[ '' -gt 0 ]]
+++ [[ '' == false ]]
git config --get branch.${current_branch}.rebase 2> /dev/null
++++ git config --get branch.feature/sandbox.rebase
+++ local will_rebase=
git stash list -n1 2> /dev/null | wc -l
++++ git stash list -n1
++++ wc -l
+++ local 'number_of_stashes=       0'
+++ [[        0 -gt 0 ]]
custom_build_prompt ${enabled:-true} ${current_commit_hash:-""} ${is_a_git_repo:-false} ${current_branch:-""} ${detached:-false} ${just_init:-false} ${has_upstream:-false} ${has_modifications:-false} ${has_modifications_cached:-false} ${has_adds:-false} ${has_deletions:-false} ${has_deletions_cached:-false} ${has_untracked_files:-false} ${ready_to_commit:-false} ${tag_at_current_commit:-""} ${is_on_a_tag:-false} ${has_upstream:-false} ${commits_ahead:-false} ${commits_behind:-false} ${has_diverged:-false} ${should_push:-false} ${will_rebase:-false} ${has_stashes:-false} ${action}
++++ custom_build_prompt true c6e6b1d3c9157294e5854825b00825047fdf2f5f true feature/sandbox false false false true false false true false true false '' false false false false false false false false
++++ local enabled=true
++++ local current_commit_hash=c6e6b1d3c9157294e5854825b00825047fdf2f5f
++++ local is_a_git_repo=true
++++ local current_branch=feature/sandbox
++++ local detached=false
++++ local just_init=false
++++ local has_upstream=false
++++ local has_modifications=true
++++ local has_modifications_cached=false
++++ local has_adds=false
++++ local has_deletions=true
++++ local has_deletions_cached=false
++++ local has_untracked_files=true
++++ local ready_to_commit=false
++++ local tag_at_current_commit=
++++ local is_on_a_tag=false
++++ local has_upstream=false
++++ local commits_ahead=false
++++ local commits_behind=false
++++ local has_diverged=false
++++ local should_push=false
++++ local will_rebase=false
++++ local has_stashes=false
++++ local prompt=
++++ local 'original_prompt=\n\[\033[0;31m\]\W\[\033[0m\] [\[\e[0;31m\]feature/sandbox\[\e[0m\]]\[\033[0m\] '
++++ local 'black=\e[0;30m'
++++ local 'red=\e[0;31m'
++++ local 'green=\e[0;32m'
++++ local 'yellow=\e[0;33m'
++++ local 'blue=\e[0;34m'
++++ local 'purple=\e[0;35m'
++++ local 'cyan=\e[0;36m'
++++ local 'white=\e[0;37m'
++++ local 'background_black=\e[40m'
++++ local 'background_red=\e[41m'
++++ local 'background_green=\e[42m'
++++ local 'background_yellow=\e[43m'
++++ local 'background_blue=\e[44m'
++++ local 'background_purple=\e[45m'
++++ local 'background_cyan=\e[46m'
++++ local 'background_white=\e[47m'
++++ local 'reset=\e[0m'
++++ local 'black_on_white=\e[0;30m\e[47m'
++++ local 'yellow_on_white=\e[0;33m\e[47m'
++++ local 'red_on_white=\e[0;31m\e[47m'
++++ local 'red_on_black=\e[0;31m\e[40m'
++++ local 'black_on_red=\e[0;30m\e[41m'
++++ local 'white_on_red=\e[0;37m\e[41m'
++++ local 'yellow_on_red=\e[0;33m\e[41m'
++++ local 'omg_default_color_on=\e[0;30m\e[47m'
++++ [[ true == true ]]
++++ prompt='\e[0;30m\e[47m '
enrich_append $is_a_git_repo $omg_is_a_git_repo_symbol "${black_on_white}"
+++++ enrich_append true $'�\210\216' '\e[0;30m\e[47m'
+++++ local flag=true
+++++ local $'symbol=�\210\216'
+++++ local 'color=\e[0;30m\e[47m'
+++++ [[ true == false ]]
+++++ echo -n '\e[0;30m\e[47m  '
++++ prompt+='\e[0;30m\e[47m  '
enrich_append $has_stashes $omg_has_stashes_symbol "${yellow_on_white}"
+++++ enrich_append false $'�\204\235' '\e[0;33m\e[47m'
+++++ local flag=false
+++++ local $'symbol=�\204\235'
+++++ local 'color=\e[0;33m\e[47m'
+++++ [[ false == false ]]
+++++ symbol=' '
+++++ echo -n '\e[0;33m\e[47m   '
++++ prompt+='\e[0;33m\e[47m   '
enrich_append $has_untracked_files $omg_has_untracked_files_symbol "${red_on_white}"
+++++ enrich_append true $'�\205\227' '\e[0;31m\e[47m'
+++++ local flag=true
+++++ local $'symbol=�\205\227'
+++++ local 'color=\e[0;31m\e[47m'
+++++ [[ true == false ]]
+++++ echo -n '\e[0;31m\e[47m  '
++++ prompt+='\e[0;31m\e[47m  '
enrich_append $has_modifications $omg_has_modifications_symbol "${red_on_white}"
+++++ enrich_append true $'�\230' '\e[0;31m\e[47m'
+++++ local flag=true
+++++ local $'symbol=�\230'
+++++ local 'color=\e[0;31m\e[47m'
+++++ [[ true == false ]]
+++++ echo -n '\e[0;31m\e[47m  '
++++ prompt+='\e[0;31m\e[47m  '
enrich_append $has_deletions $omg_has_deletions_symbol "${red_on_white}"
+++++ enrich_append true $'�\205�' '\e[0;31m\e[47m'
+++++ local flag=true
+++++ local $'symbol=�\205�'
+++++ local 'color=\e[0;31m\e[47m'
+++++ [[ true == false ]]
+++++ echo -n '\e[0;31m\e[47m  '
++++ prompt+='\e[0;31m\e[47m  '
enrich_append $has_adds $omg_has_adds_symbol "${black_on_white}"
+++++ enrich_append false $'�\205�' '\e[0;30m\e[47m'
+++++ local flag=false
+++++ local $'symbol=�\205�'
+++++ local 'color=\e[0;30m\e[47m'
+++++ [[ false == false ]]
+++++ symbol=' '
+++++ echo -n '\e[0;30m\e[47m   '
++++ prompt+='\e[0;30m\e[47m   '
enrich_append $has_modifications_cached $omg_has_cached_modifications_symbol "${black_on_white}"
+++++ enrich_append false $'�\223' '\e[0;30m\e[47m'
+++++ local flag=false
+++++ local $'symbol=�\223'
+++++ local 'color=\e[0;30m\e[47m'
+++++ [[ false == false ]]
+++++ symbol=' '
+++++ echo -n '\e[0;30m\e[47m   '
++++ prompt+='\e[0;30m\e[47m   '
enrich_append $has_deletions_cached $omg_has_cached_deletions_symbol "${black_on_white}"
+++++ enrich_append false $'�\201' '\e[0;30m\e[47m'
+++++ local flag=false
+++++ local $'symbol=�\201'
+++++ local 'color=\e[0;30m\e[47m'
+++++ [[ false == false ]]
+++++ symbol=' '
+++++ echo -n '\e[0;30m\e[47m   '
++++ prompt+='\e[0;30m\e[47m   '
enrich_append $ready_to_commit $omg_ready_to_commit_symbol "${red_on_white}"
+++++ enrich_append false $'�\206\237' '\e[0;31m\e[47m'
+++++ local flag=false
+++++ local $'symbol=�\206\237'
+++++ local 'color=\e[0;31m\e[47m'
+++++ [[ false == false ]]
+++++ symbol=' '
+++++ echo -n '\e[0;31m\e[47m   '
++++ prompt+='\e[0;31m\e[47m   '
++++ prompt='\e[0;30m\e[47m \e[0;30m\e[47m  \e[0;33m\e[47m   \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;31m\e[47m    \e[0;37m\e[41m \e[0;30m\e[41m'
++++ [[ false == true ]]
++++ [[ false == false ]]
enrich_append true "-- ${omg_not_tracked_branch_symbol}  --  (${current_branch})" "${black_on_red}"
+++++ enrich_append true '--   --  (feature/sandbox)' '\e[0;30m\e[41m'
+++++ local flag=true
+++++ local 'symbol=--   --  (feature/sandbox)'
+++++ local 'color=\e[0;30m\e[41m'
+++++ [[ true == false ]]
+++++ echo -n '\e[0;30m\e[41m--   --  (feature/sandbox)  '
++++ prompt+='\e[0;30m\e[41m--   --  (feature/sandbox)  '
enrich_append ${is_on_a_tag} "${omg_is_on_a_tag_symbol} ${tag_at_current_commit}" "${black_on_red}"
+++++ enrich_append false ' ' '\e[0;30m\e[41m'
+++++ local flag=false
+++++ local 'symbol= '
+++++ local 'color=\e[0;30m\e[41m'
+++++ [[ false == false ]]
+++++ symbol=' '
+++++ echo -n '\e[0;30m\e[41m   '
++++ prompt+='\e[0;30m\e[41m   '
++++ prompt+='\e[0;31m\e[40m\e[0m\n'
eval_prompt_callback_if_present
+++++ eval_prompt_callback_if_present
+++++ function_exists omg_prompt_callback
+++++ declare -f -F omg_prompt_callback
+++++ return 1
++++ prompt+=
++++ prompt+='\n\[\033[0;31m\]\W\[\033[0m\]\[\033[0m\] '
++++ echo '\e[0;30m\e[47m \e[0;30m\e[47m  \e[0;33m\e[47m   \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;31m\e[47m    \e[0;37m\e[41m \e[0;30m\e[41m\e[0;30m\e[41m--   --  (feature/sandbox)  \e[0;30m\e[41m   \e[0;31m\e[40m\e[0m\n\n\[\033[0;31m\]\W\[\033[0m\]\[\033[0m\] '
+++ echo '\e[0;30m\e[47m \e[0;30m\e[47m  \e[0;33m\e[47m   \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;31m\e[47m    \e[0;37m\e[41m \e[0;30m\e[41m\e[0;30m\e[41m--   --  (feature/sandbox)  \e[0;30m\e[41m   \e[0;31m\e[40m\e[0m\n\n\[\033[0;31m\]\W\[\033[0m\]\[\033[0m\] '
++ PS1='\e[0;30m\e[47m \e[0;30m\e[47m  \e[0;33m\e[47m   \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;31m\e[47m  \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;30m\e[47m   \e[0;31m\e[47m    \e[0;37m\e[41m \e[0;30m\e[41m\e[0;30m\e[41m--   --  (feature/sandbox)  \e[0;30m\e[41m   \e[0;31m\e[40m\e[0m\n\n\[\033[0;31m\]\W\[\033[0m\]\[\033[0m\] '
