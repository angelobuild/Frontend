#!/usr/bin/env bash

{
  l_output2=""
  l_uidmin="$(awk '/^\s*UID_MIN/{print $2}' /etc/login.defs)"

  file_test_fix() {
    l_op2=""
    l_fuser="root"
    l_fgroup="root"

    if [ $(( l_mode & perm_mask )) -gt 0 ]; then
      l_op2="$l_op2\n  - Permissão: \"$l_mode\" deve ser \"$maxperm\" ou mais restritiva\n   - Corrigindo permissões"
      chmod "$l_rperms" "$l_fname"
    fi

    if [[ ! "$l_user" =~ $l_auser ]]; then
      l_op2="$l_op2\n  - Dono atual: \"$l_user\" deveria ser \"${l_auser//|/ ou }\"\n   - Corrigindo dono para \"$l_fuser\""
      chown "$l_fuser" "$l_fname"
    fi

    if [[ ! "$l_group" =~ $l_agroup ]]; then
      l_op2="$l_op2\n  - Grupo atual: \"$l_group\" deveria ser \"${l_agroup//|/ ou }\"\n   - Corrigindo grupo para \"$l_fgroup\""
      chgrp "$l_fgroup" "$l_fname"
    fi

    [ -n "$l_op2" ] && l_output2="$l_output2\n - Arquivo: \"$l_fname\":$l_op2\n"
  }

  unset a_file && a_file=()
  while IFS= read -r -d $'\0' l_file; do
    [ -e "$l_file" ] && a_file+=("$(stat -Lc '%n^%#a^%U^%u^%G^%g' "$l_file")")
  done < <(find -L /var/log -type f -perm /0137 -o ! -user root -o ! -group root -print0)

  while IFS="^" read -r l_fname l_mode l_user l_uid l_group l_gid; do
    l_bname="$(basename "$l_fname")"

    case "$l_bname" in
      lastlog | lastlog.* | wtmp* | btmp* | README)
        perm_mask='0113'
        maxperm="$(printf '%o' $((0777 & ~$perm_mask)))"
        l_rperms="ug-x,o-wx"
        l_auser="root"
        l_agroup="(root|utmp)"
        ;;

      secure | auth.log | syslog | messages)
        perm_mask='0137'
        maxperm="$(printf '%o' $((0777 & ~$perm_mask)))"
        l_rperms="u-x,g-wx,o-rwx"
        l_auser="(root|syslog)"
        l_agroup="(root|adm)"
        ;;

      SSSD | sssd)
        perm_mask='0117'
        maxperm="$(printf '%o' $((0777 & ~$perm_mask)))"
        l_rperms="ug-x,o-rwx"
        l_auser="(root|SSSD)"
        l_agroup="(root|SSSD)"
        ;;

      gdm | gdm3)
        perm_mask='0117'
        maxperm="$(printf '%o' $((0777 & ~$perm_mask)))"
        l_rperms="ug-x,o-rwx"
        l_auser="root"
        l_agroup="(root|gdm|gdm3)"
        ;;

      *.journal | *.journal~)
        perm_mask='0137'
        maxperm="$(printf '%o' $((0777 & ~$perm_mask)))"
        l_rperms="u-x,g-wx,o-rwx"
        l_auser="root"
        l_agroup="(root|systemd-journal)"
        ;;

      *)
        perm_mask='0137'
        maxperm="$(printf '%o' $((0777 & ~$perm_mask)))"
        l_rperms="u-x,g-wx,o-rwx"
        l_auser="(root|syslog)"
        l_agroup="(root|adm)"

        if [ "$l_uid" -lt "$l_uidmin" ] && [ -z "$(awk -v grp="$l_group" -F: '$1==grp {print $4}' /etc/group)" ]; then
          [[ ! "$l_user" =~ $l_auser ]] && l_auser="(root|syslog|$l_user)"
          if [[ ! "$l_group" =~ $l_agroup ]]; then
            l_tst=""
            while read -r l_duid; do
              [ "$l_duid" -ge "$l_uidmin" ] && l_tst=failed
            done <<< "$(awk -F: '$4=='"$l_gid"' {print $3}' /etc/passwd)"
            [ "$l_tst" != "failed" ] && l_agroup="(root|adm|$l_group)"
          fi
        fi
        ;;
    esac

    file_test_fix
  done <<< "$(printf '%s\n' "${a_file[@]}")"

  unset a_file

  if [ -z "$l_output2" ]; then
    echo -e "- Todos os arquivos em /var/log possuem permissões e donos apropriados.\n  - Nenhuma alteração necessária.\n"
  else
    echo -e "\n$l_output2"
  fi
}
